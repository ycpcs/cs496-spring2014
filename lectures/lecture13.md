---
layout: default
title: "Lecture 13: Basic Android 3D Graphics"
---

The Android platform provides 3D graphics functionality through the [OpenGL ES 2.0](http://www.khronos.org/opengles/2_X/) embedded graphics API. (Note that OpenGL 2.0 is supported for Android version 2.2 and higher.) This API provides much of the standard OpenGL functionality through programmable shaders. To utilize OpenGL ES, we will subclass GLSurfaceView that will contain the renderer and allow for any user interaction with the application. Additionally, we will create a class that implements the GLSurfaceView.Renderer interface which will contain the OpenGL rendering code. One major difference between OpenGL ES and the OpenGL graphics learned in CS370 is that all objects must be stored as *triangles* in a *vertex buffer* that is passed to the shaders for rendering (i.e. we cannot make geometry on the fly with calls to *glVertex3f()*, etc.) However, there is still full support for creating various projection modes (orthographic or perspective), positioning a camera in the scene (similar to *gluLookAt()*), and applying all the standard transformations on objects (rotations, translations, and scalings). There is also full support for lighting, blending, and textures using similar procedures (via shaders) as OpenGL, see the following [reference](http://www.khronos.org/opengles/sdk/docs/reference_cards/OpenGL-ES-2_0-Reference-card.pdf).

The material presented in this lecture are based on the Android [OpenGL tutorial](http://developer.android.com/resources/tutorials/opengl/opengl-es20.html) and thus is simply to provide a basic framework for OpenGL graphics.

**NOTE:** OpenGL is **NOT** currently supported by the emulator, thus you will need to have an OpenGL capable device to run the code in this lecture.

OpenGL Activity
===============

The activity for an OpenGL application will simply contain a [GLSurfaceView](http://developer.android.com/reference/android/opengl/GLSurfaceView.html) object which will be set to its content view. Hence basic code might be:

    public class OpenGLExample extends Activity {
       private GLSurfaceView mGLView;

       /** Called when the activity is first created. */
       @Override
       public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);

          // Create GLSurfaceView
          mGLView = new OpenGLSurfaceView(this);
          setContentView(mGLView);
       }

       @Override
       protected void onPause() {
          super.onPause();
          // The following call pauses the rendering thread.
          // If your OpenGL application is memory intensive,
          // you should consider de-allocating objects that
          // consume significant memory here.
          mGLView.onPause();
       }

       @Override
       protected void onResume() {
          super.onResume();
          // The following call resumes a paused rendering thread.
          // If you de-allocated graphic objects for onPause()
          // this is a good place to re-allocate them.
          mGLView.onResume();
       }
    }   

The *onPause()* and *onResume()* methods can be used to deallocate/allocate any graphics objects when the application is suspended/resumed.

Also since the application needs OpenGL functionality, we must declare this in the *AndroidManifest.xml*

    <!-- Tell the system this app requires OpenGL ES 2.0. -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

OpenGL surface view
===================

The surface view class will subclass *GLSurfaceView* and will simply create a renderer object (and eventually handle any touch events). Hence a basic implementation might be:

    public class OpenGLSurfaceView extends GLSurfaceView {
       private OpenGLRenderer mRenderer;

       public OpenGLSurfaceView(Context context){
          super(context);
          // Create an OpenGL ES 2.0 context.
          setEGLContextClientVersion(2);

          // set the mRenderer member
          mRenderer = new OpenGLRenderer();
          setRenderer(mRenderer);

          // Render the view only when there is a change
          setRenderMode(GLSurfaceView.RENDERMODE_WHEN_DIRTY);
       }
    }

OpenGL renderer
===============

The majority of the work is done in a class that implement the [GLSurfaceView.Renderer](http://developer.android.com/reference/android/opengl/GLSurfaceView.Renderer.html) interface. This interface requires implementation of three methods - *onSurfaceCreated()*, *onSurfaceChanged()*, and *onDrawFrame()*. Each of these methods will be explained in subsequent sections.

*onSurfaceCreated()*
--------------------

This method is called whenever the OpenGL surface is created. Thus it is responsible for tasks such as initialization of the vertex buffer and loading/compiling the shaders.

*Vertex buffers*

Since OpenGL ES 2.0 works only with vertex buffers that store vertices for *triangles*, a basic implementation of an initialization method might be:

    private FloatBuffer mTriangleVB;

    public void init() {

        // Create geometry via triangles
       float triangleCoords[] = {
          // X, Y, Z
          -0.5f, -0.25f, 0,
          0.5f, -0.25f, 0,
          0.0f,  0.559016994f, 0
       }; 

       // initialize vertex Buffer for triangle  
       ByteBuffer vbb = ByteBuffer.allocateDirect(
          // (# of coordinate values * 4 bytes per float)
          triangleCoords.length * 4); 

       // Use native byte order
       vbb.order(ByteOrder.nativeOrder());

       // Create floating point buffer from byte buffer
       mTriangleVB = vbb.asFloatBuffer();

       // Add vertices to buffer
       mTriangleVB.put(triangleCoords);

       // Set the buffer to first vertex
       mTriangleVB.position(0); 
    }

Alternatively, the vertices could be obtained by parsing a resource file generated by a 3D modeling program such as [Blender](http://www.blender.org/).

*Shader code*

OpenGL ES 2.0 uses shaders for all the rendering operations. Hence there needs to be at least one vertex and one fragment shader which are compiled and linked into a shader program. One basic way to include the shader source code is via local strings such as:

> private final String vertexShaderCode =  
> // This matrix member variable provides a hook to manipulate // the coordinates of the objects that use this vertex shader "uniform mat4 uMVPMatrix; n" +
>
> "attribute vec4 vPosition; n" + "void main(){ n" +
>
> // the matrix must be included as a modifier of gl\_Position " gl\_Position = uMVPMatrix \* vPosition; n" + "} n";
>
> private final String fragmentShaderCode =  
> "precision mediump float; n" + "void main(){ n" + " gl\_FragColor = vec4 (0.63671875, 0.76953125, 0.22265625, 1.0); n" + "} n";
>
This shader code should look familiar to those of you with GLSL experience as it is simply a basic vertex shader (takes the vertex as an attribute variable and applies the model-view-projection matrix transformation) and basic fragment shader (sets the fragment color to a constant value). The shaders can then be compiled into a shader object with the following method

    private int loadShader(int type, String shaderCode){

       // Create shader object of appropriate type
       int shader = GLES20.glCreateShader(type); 

       // Compile shader source
       GLES20.glShaderSource(shader, shaderCode);
       GLES20.glCompileShader(shader);

       return shader;
    }

*Creating the surface*

Hence the *onSurfaceCreated()* method will then create the vertex buffer (via the *init()* method) and create the shader program as

    private int mProgram;
    private int maPositionHandle;
    private int muMVPMatrixHandle;

    public void onSurfaceCreated(GL10 unused, EGLConfig config) {       
       // Set the background frame color
       GLES20.glClearColor(0.5f, 0.5f, 0.5f, 1.0f);
       init();

       // Load/Compile shaders from shader source
       int vertexShader = loadShader(GLES20.GL_VERTEX_SHADER, vertexShaderCode);
       int fragmentShader = loadShader(GLES20.GL_FRAGMENT_SHADER, fragmentShaderCode);

       // Create shader program object
       mProgram = GLES20.glCreateProgram();

       // Attach vertex and fragment shader to program
       GLES20.glAttachShader(mProgram, vertexShader);
       GLES20.glAttachShader(mProgram, fragmentShader);

       // Link shaders to create shader program
       GLES20.glLinkProgram(mProgram);

       // Get vertex shader variable reference
       maPositionHandle = GLES20.glGetAttribLocation(mProgram, "vPosition");
       muMVPMatrixHandle = GLES20.glGetUniformLocation(mProgram, "uMVPMatrix");
    }

*onSurfaceChanged()*
--------------------

The *onSurfaceChanged()* method is similar to the reshape methods from CS370. Hence it will control adjusting the viewport and/or projection matrix and camera location. Thus the method might be

    private float[] mProjMatrix = new float[16];
    private float[] mVMatrix = new float[16];

    public void onSurfaceChanged(GL10 unused, int width, int height) {
       // Set viewport to new extents
       GLES20.glViewport(0, 0, width, height);

       // Compute aspect ratio for proper scaling
       float ratio = (float) width / height;

       // Create perspective projection matrix
       Matrix.frustumM(mProjMatrix, 0, -ratio, ratio, -1, 1, 3, 7);

       // Set camera modelview matrix
       Matrix.setLookAtM(mVMatrix, 0, 0, 0, -3, 0f, 0f, 0f, 0f, 1.0f, 0.0f);
    }

*onDrawFrame()*
---------------

All of the rendering is then accomplished in the *onDrawFrame()* method. Thus this method will be responsible for computing any local transformations and setting the appropriate shader variables. It will also tell the shader which vertices to use in drawing the objects (through the *glDrawArray()* method). Hence a basic method might be

    public float mAngle;
    private float[] mMMatrix = new float[16];
    private float[] mMVPMatrix = new float[16];

    public void onDrawFrame(GL10 unused) {    
       // Clear frame and depth buffers
       GLES20.glClear(GLES20.GL_COLOR_BUFFER_BIT | GLES20.GL_DEPTH_BUFFER_BIT);

       // Select shader program
       GLES20.glUseProgram(mProgram);

       // Attaches vertex buffer to shader
       GLES20.glVertexAttribPointer(maPositionHandle, 3, GLES20.GL_FLOAT, false, 12, mTriangleVB);
       GLES20.glEnableVertexAttribArray(maPositionHandle);

       // Compute local transformation (rotation) matrix
       Matrix.setRotateM(mMMatrix, 0, mAngle, 0, 0, 1.0f);

       // Add local transformation into modelview matrix
       Matrix.multiplyMM(mMVPMatrix, 0, mVMatrix, 0, mMMatrix, 0);

       // Add modelview matrix to projection matrix
       Matrix.multiplyMM(mMVPMatrix, 0, mProjMatrix, 0, mMVPMatrix, 0);

       // Pass modelview-projection matrix to shader
       GLES20.glUniformMatrix4fv(muMVPMatrixHandle, 1, false, mMVPMatrix, 0);

       // Draw geometry
       GLES20.glDrawArrays(GLES20.GL_TRIANGLES, 0, 3);    
    }

Refer to the [OpenGL ES documentation](http://developer.android.com/reference/android/opengl/GLES20.html) for more information about the various OpenGL functions.

Handling user input (touch events)
==================================

Similar to 2D graphics, we can implement a touch event handler in the *GLSurfaceView* subclass to allow for user interaction with the application. For example, an *onTouchEvent()* method to adjust the rotation of the object based on swipe gestures might be

    private final float TOUCH_SCALE_FACTOR = 180.0f / 320;
    private float mPreviousX;
    private float mPreviousY;

    @Override 
    public boolean onTouchEvent(MotionEvent event) {

       // Get locations from event
       float x = event.getX();
       float y = event.getY();

       switch (event.getAction()) {
          case MotionEvent.ACTION_MOVE:
             // Compute swipe distance
             float dx = x - mPreviousX;
             float dy = y - mPreviousY;

             // Change direction of rotation above the mid-line
             if (y > getHeight() / 2) {
                dx = dx * -1 ;
             }

             // Change direction of rotation to left of the mid-line
             if (x < getWidth() / 2) {
                dy = dy * -1 ;
             }

             // Update rotation angle
             mRenderer.mAngle += (dx + dy) * TOUCH_SCALE_FACTOR;

             // Render updated frame
             requestRender();
       }

       // Store current locations
       mPreviousX = x;
       mPreviousY = y;

       return true;
    }   
