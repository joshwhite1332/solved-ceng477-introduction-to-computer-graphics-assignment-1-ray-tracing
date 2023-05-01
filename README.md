Download Link: https://assignmentchef.com/product/solved-ceng477-introduction-to-computer-graphics-assignment-1-ray-tracing
<br>
<h2>Objectives</h2>

Ray tracing is a fundamental rendering algorithm. It is commonly used in applications in which the quality of the images are more important than the time it takes to create them such as architectural simulations and animations. You are going to implement a very basic ray tracer simulating the propagation of the light in vacuum in this assignment.

<h2>Specifications</h2>

<ul>

 <li>A template for implementation of a basic ray tracer is provided to you. This template consists of header files of the classes you will need, declarations of the public methods and public variables, implementation of an XML parser, and a ppm file saver. Explanation of the files are not provided here. Take a look at the files before starting, read the comments carefully, and try to understand how you can use the provided utilites. You are allowed to add your own variables and methods to only <em>private </em>area of the classes, not to <em>public </em> The assignment is implementable in this way, you do not actually need to add more public variables and methods to the classes.</li>

 <li>Your program will take a scene as XML file as command line input. You should save the output image as a ppm file. Parsing the XML file, and a method to save the image as ppm file is implemented for you. Some sample scene files are provided to you in <em>inputs </em> Correct outputs of those scenes are also provided in <em>outputs </em>directory.</li>

 <li>The scene file may contain multiple camera configurations. You should render as many images as the number of cameras. The output filenames for each camera is also specified in the XML file.</li>

 <li>You will have at most 15 minutes to render scenes for each input file on Inek machines. Programs exceeding this limit will be killed and will be assumed to have produced no image.</li>

 <li>You should use Blinn-Phong shading model for the specular shading computations.</li>

 <li>You will implement two types of light sources: ambient and point. Although there will be only one ambient light, there may be multiple point light sources. The intensity of these lights will be given as (r, g, b) triplets.</li>

</ul>

Point lights will be defined by their intensity (power per unit solid angle). The irradiance due to such a light source falls off as inversely proportional to the squared distance from the light source. To simulate this effect, you must compute the irradiance at a distance of <em>d </em>from a point light as:

where <em>I </em>is the original light intensity (a triplet rgb value given in the XML file) and <em>E</em>(<em>d</em>) is the irradiance at a distance of <em>d </em>from the light source.

<h2>Scene File</h2>

The scene file will be formatted as a XML file. In this file, there may be different number of materials, vertices, triangles, spheres, lights, and cameras. Each of these are defined by a unique integer id. The ids for each type of element will start from one and increase sequentially. Explanations for each XML tag are provided below:

<ul>

 <li><strong>BackgroundColor: </strong>Specifies rgb value of the background. If a ray sent through a pixel does not hit any object, the pixel will be set to this color.</li>

 <li><strong>ShadowRayEpsilon: </strong>When a ray hits an object, you are going to send a shadow ray from the intersection point to each point light source to decide whether the hit point is in shadow or not. Due to floating point precision errors, sometimes the shadow ray hits the same object even if it should not. Therefore, you must use this small <em>ShadowRayEpsilon </em>value, which is a floating point number, to move the intersection point a bit further so that the shadow ray does not intersect the same object again. Note that <em>ShadowRayEpsilon </em>value can also be used to avoid self-intersections while casting reflection rays from the interaction point.</li>

 <li><strong>MaxRecursionDepth: </strong>Specifies how many bounces the ray makes off of mirror-like objects. Applicable only when a material has nonzero <em>MirrorReflectance </em> Primary rays are assumed to start with zero bounce count.</li>

 <li><strong>Camera:</strong>

  <ul>

   <li><strong>Position: </strong>Defines the coordinates of the camera.</li>

   <li><strong>Gaze: </strong>Defines the direction that the camera is looking at. You must assume that the gaze vector of the camera is always perpendicular to the image plane.</li>

   <li><strong>Up: </strong>Defines the up vector for the camera.</li>

   <li><strong>NearPlane: </strong>Defines the coordinates of the image plane with left, right, bottom, top parameters.</li>

   <li><strong>NearDistance: </strong>Defines the distance to the image plane to the camera.</li>

   <li><strong>ImageResolution: </strong>Defines the resolution of the image as width, and height.</li>

   <li><strong>ImageName: </strong>Defines the name of the output file.</li>

  </ul></li>

</ul>

Cameras defined in this assignment will be right-handed. The mapping of Up and Gaze vectors to the camera terminology used in the course slides is given as:

<em>Up </em>= <em>v</em>

<em>Gaze </em>= −<em>w u </em>= <em>v</em>×<em>w </em><strong>AmbientLight: </strong>Defined by an intensity rgb triplet. This is the amount of light receive by each object even if the object is in shadow.

<ul>

 <li><strong>PointLight: </strong>Defined by a position (triplet as x, y, z coordinates) and intensity (rgb triplet).</li>

 <li><strong>Material: </strong>A material can be defined with ambient, diffuse, specular, and mirror reflectance properties for each color channel. Values are floats between 0.0 and 1.0. <em>PhongExponent </em>defines the specularity exponent in Blinn-Phong shading. <em>MirrorReflectance </em>represents the degree of the mirrorness of the material. If this value is not zero, you must cast new rays and scale the resulting color value with <em>MirrorReflectance </em></li>

 <li><strong>VertexData: </strong>Each line contains a vertex whose x, y, and z coordinates are given as floating point values, respectively.</li>

 <li><strong>Triangle: </strong>A triangle is represented by Material and Indices attributes. Material attribute represents the material id. Indices are the integer vertex ids of the vertices that construct the triangle (Note that vertices are 1-based, i.e., the index of the first vertex in the <em>VertexData </em>field is 1 not 0.). Vertices are given in counter-clockwise order, which is important when you want to calculate the normals of the triangles. Counter-clockwise order means that if you close you right-hand following the order of the vertices, your thumb points in the direction of the surface normal.</li>

 <li><strong>Sphere: </strong>A sphere is represented by Material, Center, and Radius attributes. Material attribute represents the material id. Center represents the vertex id of the point which is the center of the sphere (Note that vertices are 1-based, i.e., the index of the first vertex in the <em>VertexData </em>field is 1 not 0.). Radius attribute is the radius of the sphere.</li>

 <li><strong>Mesh: </strong>Each mesh is composed of several faces. A face is actually a triangle which contains three vertices. When defining a mesh, each line in <em>Faces </em>attribute defines a triangle. That is, each line is represented by three integer vertex ids given in counter-clockwise order (Note that vertices are 1-based, i.e., the index of the first vertex in the <em>VertexData </em>field is 1 not 0.). Material attribute represents the material id of the mesh.</li>

</ul>

You can open the sample XML files given to you with a text editor to see and understand the scene

<h2>Regulations</h2>

<ul>

 <li><strong>Programming Language: </strong>The programming language for this assignment is C/C++ (You can use C++11).</li>

 <li><strong>External Libraries: </strong>You can use external library such as Eigen for matrix/vector algebra, like adding, subtracting, multiplying (both cross and dot product) vectors. If you do so, update <em>Vector3f </em>variables inside of the files according to the ones your external library uses.</li>

 <li><strong>Class Interfaces: </strong>The classes provided to you has public variables and methods. You have to implement thos methods. You can define you own variables and methods only as <em>private </em>area of the classes. You are not allowed to add new variables and methods to <em>public </em>area of the classes. Otherwise, you will lose considerable amount of points.</li>

 <li><strong>Grouping: </strong>You can team-up with another student. There is a <em>txt </em>file among the files provided to you. Write your student ids into that file. Only one member of the team may upload the assignment, upload of the assignment by two of the members of the team is not necessary.</li>

 <li><strong>Submission: </strong>Submission will be done via OdtuClass.</li>

</ul>

<strong>Late Policy: </strong>You can submit your codes up to 3 days late. Each late day will be deduced from the total 7 days credits for the semester. However, if you fail to submit even after 3 days, you will get 0 regardless of how many late credits you may have left. If you submit and still get 0, you cannot claim back your late days.

<ul>

 <li><strong>Bonus Grades:</strong></li>

 <li>You can get 10 points bonus if your ray tracer is among the fastest <em>N </em>ray tracers to be determined by our benchmarks. Note that your outputs should be correct to be eligible for this bonus.</li>

 <li>You can get 10 points bonus if you create and share interesting XML scene files. These should not be simple scenes as we ahave already shared such scenes with you. Interesting scenes to get bonus will be determined by the course instructors and assistants.</li>

 <li><strong>Evaluation: </strong>Your codes will be evaluated on department Inek machines. Be careful about this if you are implementing your homework in other operating systems such as Windows or macOS. Although some simple scenes are provided to you with the correct outputs, other scenes with different camera positions may be used while evaluating your code.</li>

</ul>

A makefile is provided to you to compile the code, the output of the makefile is named as <em>raytracer.out</em>. While evaluating your assignment, your code will be compiled only with the following

command:

<em>$make</em>

After that, a scene will be given to your program as a XML file as follows:

<em>$./raytracer inputs/input01.xml</em>

Your program saves the output(s) as ppm file(s) (Whose implementation is done) in the same directory with the source code files.

Stick with the evaluation specification, if the codes you will provide will not be compiled, your grade will be zero. Even if you make it work during the objection stage, you will lose points.


