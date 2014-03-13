---
layout: default
title: "Lecture 11: AJAX"
---

AJAX
====

"Traditional" web applications generate HTML on the server, and the client interacts with the application by submitting data in HTML forms using POST requests. In this model, the page must be reloaded each time the user submits a request.

Modern web applications can use a technique known as [AJAX](http://en.wikipedia.org/wiki/Ajax_(programming)), in which Javascript code running in the client web browser sends a request to the HTTP server without a page reload. The server response is received asynchronously, at which point the client user interface can be updated by the Javascript code.

The combination of AJAX and [dynamic HTML](lecture9.html) allows the creation of fully-featured user interfaces that run within the client web browser and communicate with the server when necessary.

Example: Add Numbers
====================

Let's reimplement the "Add Numbers" application from the previous lecture using AJAX. The idea is that the user interface will look the same (consisting of text fields for data entry and a button to add the numbers). The main difference is that these elements will not be embedded in an HTML form. Instead, a click handler for the button will invoke Javascript code which initiates an AJAX request with the numbers to be added. A *success callback* will then update the result when the response is received from the server. An *error callback* will display an error message if the request can't be completed.

Here is the HTML:

    <html>
      <head>
        <title>Add Numbers (AJAX)</title>

        <script
          src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"
          type="text/javascript">
        </script>

        <style type="text/css">
        td.label {
          text-align: right;
          font-weight: bold;
        }

        .error {
          color: red;
        }
        </style>

        <script type="text/javascript">
          $(document).ready(function() {
            $("#addNumbersButton").click(function() {
              $.ajax({
                type: 'POST',
                url: '/lab12/ajax/addNumbers',
                data: { first: $("#firstNumberElt").val(), second: $("#secondNumberElt").val() },
                success:
                  function(data, textStatus, jqXHR) {
                    $("#resultElt").text(data);
                  },
                error:
                  function(jqXHR, textStatus, errorThrown) {
                    $("#errorElt").text(textStatus + ": " + errorThrown);
                  },
                dataType: 'text'
              });
            });
          });
        </script>
      </head>

      <body>
        <table>
          <tr>
            <td class="label">First number:</td>
            <td><input id="firstNumberElt" type="text" size="12" /></td>
          </tr>
          <tr>
            <td class="label">Second number:</td>
            <td><input id="secondNumberElt" type="text" size="12" /></td>
          </tr>
          <tr>
            <td class="label">Result:</td>
            <td><span id="resultElt"></span></td>
          </tr>
        </table>
        <div>
          <button id="addNumbersButton">Add Numbers</button>
        </div>
        <div>
          <span id="errorElt" class="error"></span>
        </div>
      </body>
    </html>

**Important**: this is just a static HTML document! This is an interesting advantage of AJAX over the traditional form submission/page reload model: the user interface can be static HTML, which can be delivered once (not multiple times) and cached by the client.

The important part is the call to the **\$.ajax** method. This is a method provided by jQuery to simplify AJAX requests. The value passed to the method is a Javascript object whose fields describe the request. The important fields are:

-   **type** - the type of HTTP request. In the example above it's POST, but it could also be GET. Note that web browsers can only generate GET and POST requests.
-   **url** - the URL of the servlet that will be handling the request. In this case, it's the servlet handling requests to **/lab12/ajax/addNumbers**.
-   **data** - a Javascript object whose fields are parameters to be sent to the servlet, and whose values are the values of those parameters. In the example above, the parameters are called **first** and **second** and their values are the values found in the textboxes in the UI.
-   **success** - the success callback. It takes the data received from the server and sets it as text in the result span element in the UI, displaying the result of the computation to the user.
-   **error** - the error callback. Sets an error message in the error message span element in the UI.
-   **dataType** - the type of data expected from the server. In our case we're expecting plain text, but other formats jQuery supports are XML and JSON.

Here is the servlet which handles the requests:

    package edu.ycp.cs496.lab12.servlet.ajax;

    import java.io.IOException;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    import edu.ycp.cs496.lab12.controller.AddNumbersController;

    public class AddNumbersAjaxServlet extends HttpServlet {
      private static final long serialVersionUID = 1L;

      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
        doRequest(req, resp);
      }

      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
        doRequest(req, resp);
      }

      private void doRequest(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Get parameters
        Double first = getDouble(req, "first");
        Double second = getDouble(req, "second");

        // Check whether parameters are valid
        if (first == null || second == null) {
          badRequest("Bad parameters", resp);
          return;
        }

        // Use a controller to process the request
        AddNumbersController controller = new AddNumbersController();
        Double result = controller.add(first, second);

        // Send back a response
        resp.setContentType("text/plain");
        resp.getWriter().println(result.toString());
      }

      private Double getDouble(HttpServletRequest req, String name) {
        String val = req.getParameter(name);
        if (val == null) {
          return null;
        }
        try {
          return Double.parseDouble(val);
        } catch (NumberFormatException e) {
          return null;
        }
      }

      private void badRequest(String message, HttpServletResponse resp) throws IOException {
        resp.setContentType("text/plain");
        resp.setStatus(HttpServletResponse.SC_BAD_REQUEST);
        resp.getWriter().println(message);
      }
    }

The servlet is pretty simple. It handles both GET and POST requests the same way, by extracting the **first** and **second** query parameters, converting them to **Double** values, using an **AddNumbersController** to compute their sum, and sending the sum back to the client using plain text.

This servlet is a lot like the **AddNumbersServlet** from the previous lecture. The main difference is that this servlet does not have to worry about providing a user interface. In this sense it is a web service, since it provides functionality which is meant to be used by programs rather than human users.

Note also the following critical lines in the servlet above:

    AddNumbersController controller = new AddNumbersController();
    Double result = controller.add(first, second);

The AJAX servlet is using the same controller as the previous web application servlet! In general, there is no reason why you can define all of your application logic in controllers that can be reused by your web service, web application, and AJAX servlets.

Example: AJAX guessing game
===========================

Let's reimplement the guessing game using AJAX: specifically, we'll use AJAX to generate the guesses.

Here's the HTML/Javascript:

    <html>
      <head>
        <title>Guessing Game (AJAX)</title>

        <script
          src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"
          type="text/javascript">
        </script>

        <style type="text/css">
        td.label {
          text-align: right;
          font-weight: bold;
        }

        .error {
          color: red;
        }
        </style>

        <script type="text/javascript">
          window.gg = {
            // display the initial UI with a start game button
            begin : function(msg) {
              $("body").empty().append(
                '<div>' + msg + '</div>' +
                '<div><button id="start">Start game!</button></div>');
              $("#start").click(function() {
                gg.startGame();
              });
            },

            // start the game
            startGame: function() {
              // gg.game is the client-side model object.
              // The server will send back updates to the model object
              // using JSON.
              gg.game = { min: 1, max: 100 };

              gg.makeUI();
              gg.guess();
            },

            // Make the UI for displaying the current guess and allowing the
            // user to specify whether it's correct, too bigh, or too low
            makeUI: function() {
              $("body").empty().append(
                '<div><span id="guess">&nbsp;</span></div>' +
                '<div>' +
                '<input id="gotIt" type="submit" value="Yes, that\'s it!" />' +
                '<input id="less" type="submit" value="No, that\'s too big" />' +
                '<input id="more" type="submit" value="No, that\'s too small" />' +
                '</div>'
              );
              $("#gotIt").click(function() {
                gg.begin("You were thinking of " + gg.game.guess);
              });
              $("#less").click(function() {
                gg.game.action = "less";
                gg.guess();
              });
              $("#more").click(function() {
                gg.game.action = "more";
                gg.guess();
              });
            },

            // Based on current min and max, ask server for the next guess
            guess: function() {
              $.ajax({
                type: 'POST',
                url: '/lab12/ajax/nextGuess',
                data: gg.game,
                success: function(data, textStatus, jqXHR) {
                  gg.game = data;
                  $("#guess").text('Are you thinking of ' + gg.game.guess + '?'); 
                },
                error: function(jxXHR, textStatus, errorThrown) {
                  alert(textStatus + ": " + errorThrown);
                },
                dataType: 'json'
              });
            },
          };

          $(document).ready(function() {
            gg.begin("Welcome to the AJAX guessing game!");
          });
        </script>
      </head>

      <body>

      </body>
    </html>

Some things to note:

-   The code creates an object called **window.gg**, which contains as fields the methdos which implement the guessing game. (Note that the Javascript **window** object serves as a global scope for Javascript code running in a web browser window.) This is a common implementation approach: this object is essentially a namespace. Because many Javascript libraries can be loaded in the context of the same HTML document, using namespaces avoids naming conflicts between their methods.
-   The user interface is built dynamically: note that the **\<body\>** element is initially empty. The **gg.begin** method creates an initial user interface by appending child elements to the **\<body\>** element.
-   The **gg.game** object is a model object for the guessing game, specifying the current min and max values. The **gg.startGame** method initializes this model object.
-   The **gg.guess** method fetches the next guess from the server by sending the **gg.game** object's values as the query parameters. The response sent back by the server is expected to be an updated model object in JSON format. Note how the success callback for the AJAX request treats its **data** parameter as an object!
-   The button handlers for the **\#less** and **\#more** buttons set an **action** parameter in the current model object. This will let the **nextGuess** servlet know that the previous guess was either too high or too low.

Here is the implementation of **NextGuessAjaxServlet**:

    package edu.ycp.cs496.lab12.servlet.ajax;

    import java.io.IOException;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    import edu.ycp.cs496.lab12.controller.GuessingGameController;
    import edu.ycp.cs496.lab12.model.GuessingGame;

    public class NextGuessAjaxServlet extends HttpServlet {
      private static final long serialVersionUID = 1L;

      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
        doRequest(req, resp);
      }

      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
        doRequest(req, resp);
      }

      private void doRequest(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
        // Read client's model
        Integer min = getInteger(req, "min");
        Integer max = getInteger(req, "max");

        if (min == null || max == null) {
          badRequest("Invalid min/max values", resp);
          return;
        }

        GuessingGame model = new GuessingGame();
        model.setMin(min);
        model.setMax(max);

        // If an action was specified, use a GuessingGameController to carry it out
        String action = req.getParameter("action");
        if (action != null) {
          GuessingGameController controller = new GuessingGameController();
          controller.setModel(model);

          if (action.equals("less")) {
            controller.setNumberIsLessThanGuess();
          } else if (action.equals("more")) {
            controller.setNumberIsGreaterThanGuess();
          }
        }

        int guess = model.getGuess();

        // Send back updated model to client
        resp.setContentType("application/json");
        resp.getWriter().println(
            "{ \"min\": " + model.getMin() +
            ", \"max\": " + model.getMax() +
            ", \"guess\": " + guess + "}" );
      }

      private Integer getInteger(HttpServletRequest req, String name) {
        String val = req.getParameter(name);
        if (val == null) {
          return null;
        }
        try {
          return Integer.parseInt(val);
        } catch (NumberFormatException e) {
          return null;
        }
      }

      private void badRequest(String message, HttpServletResponse resp) throws IOException {
        resp.setContentType("text/plain");
        resp.setStatus(HttpServletResponse.SC_BAD_REQUEST);
        resp.getWriter().println(message);
      }
    }

As with the **AddNumbersAjaxServlet**, the servlet retrieves data from the request parameters, creates model objects to represent the request, and uses a controller to process the request. As with the add numbers servlet, the controller developed for the earlier traditional web application is re-used.

> **Using MVC2 controllers to implement your application logic allows you to implement the same behavior in a web service, traditional web application, and AJAX web application.**

One interesting thing to note with this servlet: it sends back an updated version of the model object using JSON:

    resp.getWriter().println(
      "{ \"min\": " + model.getMin() +
      ", \"max\": " + model.getMax() +
      ", \"guess\": " + guess + "}" );

JSON, short for **J**ava**s**cript **O**bject **N**otation, is a way of sending Javascript objects (data only) as text. It is a convenient format for AJAX because it allows data received from the server to be used as a Javascript object.
