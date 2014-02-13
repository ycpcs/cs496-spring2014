---
layout: default
title: "Lecture 7b: UI Considerations"
---

Responsiveness
==============

One issue that all mobile developers must consider is the responsiveness of their applications. It is critical for the user experience that a mobile app's UI remain responsive even if the application itself is doing expensive, i.e. time-consuming, processing. One major place where this occurs is when the application accesses resources via the network particularly the Internet. When the application attempts to access a network resource, e.g. downloading a large image, we do not want the UI to appear "frozen" while the resource is being retrieved as it will not only create a poor user experience, but may even trigger an "Application Not Responding" dialog from the system. These dialogs are part of the Android OS to ensure that no application unnecessarily consumes system resources.

Network Access
==============

To address the issue of maintaining UI responsiveness, any significant time consuming task should be relegated to a background task (or even a separate [process/thread](http://developer.android.com/guide/components/processes-and-threads.html)). Beginning with Android 3.0 ([Honeycomb](http://developer.android.com/reference/android/os/Build.VERSION_CODES.html#HONEYCOMB)) but recommended for all versions, Android prevents any network access on the main UI thread and will throw a [NetworkOnMainThreadException](http://developer.android.com/reference/android/os/NetworkOnMainThreadException.html) if one is attempted. The Android SDK makes it simple to handle this situation through delegating the network tasks to a worker thread via subclassing the [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) class.

AsyncTask
---------

The [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) class works by utilizing three generic types called **Params**, **Progress**, and **Result** to implement four methods **onPreExecute()**, **doInBackground()**, **onProgressUpdate()**, and **onPostExecute()**. Of these four, only **doInBackground()** is required to be implemented.

A subclass of **AsyncTask** can be created as

	public class MyTask extends AsyncTask<ParamsType, ProgressType, ResultType> {
	
		@Override
		protected ResultType doInBackground(Params...params) {
			// Do main work here
			
			return something of type ResultType;
		}
		
		@Override
		protected void onProgressUpdate(ProgressType...progparams) {
			// Update UI progress indicator
		}
		
		@Override
		protected void onPostExecute(ResultType result) {
			// Do something when task is completed
			// such as update UI widget
		}
	}
	
The three generic type parameters can be specified as needed (or even **Void** if they are not used.

Then to start the background task, e.g. in the click handler when a button is pressed, simply create a new instance of the class and call the **execute()** method passing any needed values of type **ParamsType**. For example

	ParamsType p1, p2, p3;
	MyTask mt = new MyTask();
	mt.execute(p1, p2, p3);
	
Note that in the **doInBackground()** method, the **params** parameter is an *array* of values of type **ParamsType**.

If the result of the task is needed, use the **get()** method for the **MyTask** object

	ResultType result = mt.get();
	
Network Example
---------------

To illustrate using [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) we will consider the network portion of the geocoding lab ([Lab05b](../labs/lab05b.html)). Since the network access is part of the controller class, we can simply have the controller subclass **AsyncTask** as follows

	public class MobileController extends AsyncTask<String, Void, Result> {

		@Override
		protected Result doInBackground(String... params) {
			return getGeoDistance(params[0],params[1],params[2],params[3]);
		}

		private Result getGeoDistance(String firstStreet, String firstZip, String secondStreet, String secondZip) {
		
			// Make get requests for addresses
			// PostalCode p1 = makeGeoGetRequest(firstStreet, firstZip);
		
			Result r = null;
			// Compute result if both requests returned valid info, null otherwise
			// ...
		
			return r;
		}
	
		private PostalCodes makeGeoGetRequest(String street, String zip)
		{
			try {
				// Create HTTP client
				HttpClient client = new DefaultHttpClient();
	
				// Create list of request paramater/value pairs
				List<NameValuePair> params = new ArrayList<NameValuePair>();
				params.add(new BasicNameValuePair("postalcode", zip));
				params.add(new BasicNameValuePair("placeName", street));
				params.add(new BasicNameValuePair("country", "US"));
				params.add(new BasicNameValuePair("username","ycpcs_cs496"));
			
				// Construct URI
				URI uri;
				uri = URIUtils.createURI("http", "api.geonames.org", -1, "/postalCodeSearchJSON", 
							URLEncodedUtils.format(params, "UTF-8"), null);
	
				// Make get request
				HttpGet request = new HttpGet(uri);
				HttpResponse response;
				response = client.execute(request);
			
				if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
					// Copy the response body to a string
					HttpEntity entity = response.getEntity();
				
					// Parse JSON
					return JSON.getObjectMapper().readValue(entity.getContent(), PostalCodes.class);
				}
			}
			catch (Exception e) {
				// Handle any exceptions
				e.printStackTrace();
			}
		
			// Return null if invalid response
			return null;
		}

	}
		
Note that in this case the controller will require four parameters of type **String** and returns a value of type **Result** (since we are not using the progress method it has type **Void**). We then simply extract the four strings from the **params** array and pass them to a helper method.

The click handler then becomes

	// Instantiate controller and compute distance
	MobileController mc = new MobileController();
	mc.execute(firstStreet.getText().toString(), firstZip.getText().toString(),
			   secondStreet.getText().toString(), secondZip.getText().toString());
	Result r = mc.get();

	// If result is valid, display result
	if (r != null) {
	   distanceLabel.setText(String.format("%.2f miles",r.getValue()));
	} else {
	   distanceLabel.setText("Invalid inputs");
	}

