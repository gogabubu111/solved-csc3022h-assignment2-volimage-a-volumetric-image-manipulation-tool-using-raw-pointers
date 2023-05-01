Download Link: https://assignmentchef.com/product/solved-csc3022h-assignment2-volimage-a-volumetric-image-manipulation-tool-using-raw-pointers
<br>
VolImage: A Volumetric Image Manipulation Tool using raw pointers

Your task is to build a volumetric image tool, which can load a “stack” of 2D images and manipulate them as a group. Volumetric images are produced by many instruments, including radio astronomy telescopes and MRI machines used in medical imaging.

To simplify this problem, you will only have to load a sequence of 2D unsigned byte (values from 0..255) buffers, one for each image slice. These will be provided for you, as a sequence of binary (“.raw”) files with a header file that provides the image dimensions. So, for example, an image sequence called “scan” would have a file “scan.dat” (the header file) and a sequence of images numbered as follows: “scan0.raw”, “scan1.raw”, “scan2.raw” etc. You can determine the number of images to open and read by first opening the header file. Your application will be invoked as follows on the command line:

volimage &lt;imageBase&gt; [-d i j output_file_name] [-x i output_file_name]

where ’volimage’ is the name you should give your executable, <em>&lt;</em>imageBase<em>&gt;</em>is the the prefix for the file sequence ( “scan” in our example) (the angle brackets are a convention to show the argument must be provided). There are then two optional command line arguments (indicated by the [] brackets):

<ol>

 <li>-d i j output file name: compute a difference map between images i and j, and write this out to file (note: we index files from 0, see below).</li>

 <li>-x i output file name: extract and write the slice with number i and write this out to file.</li>

</ol>

You will have to parse the command line using the argc and argv values passed into main(). Remember, argc tells you the number of all items on the command line (the application name included – so “volimage scan -x 5 exoutput” would cause argc to be 5; the argv array would contain simple C-strings for each of these. argv[0] is always the application name, and argv[1] the first argument. Note: you can turn a string, such as “5”, into an integer by reading into an int variable from an istringstream constructed with the string source.

If you provide no optional flags, the application should go ahead and build the internal representation and then exit after ensuring memory is correctly cleaned up, and print the following to the console:

Number of images: (int)

Number of bytes required: (int)

The number of bytes required will be the number of bytes required to store the image data ONLY (including pointers).

If you request an operation, the same information should be displayed, along with a message stating what the operation achieved (you can determine the output message format – this is not auto-marked).

<h1>Header format</h1>

The header is a file which contains 3 integers:

width height number images

The with and height provides the pixel with and height of the images and number images tells us how many images are in the sequence. Note that white space is used to separate these numbers.

<h1>Operations</h1>

<ol>

 <li>extract: retrieve the slice with the index given and write this to an output sequencecalled “output”. You will need to generate one header, “output.dat”, with the width, height and number of slices set to 1. You will also need to write out the 2D unsigned byte array as “output.raw”.</li>

 <li>difference map: computes the difference map between “slices” i and j. Every outputpixel at coordinate (r,c) is computed as follows:</li>

</ol>

(unsigned char)(abs((float)volume[i][r][c] – (float)volume[j][r][c])/2)

You can do a difference map or extraction, but not both during the same invocation of volimage.

<strong>[extra credit: define a new operation [-g i], which extracts an image along row i of the volume, across all slices, and write this out as above. As noted above, this would be an operation that cannot be done with others]</strong>

Grading: if you do everything correctly and the program passes all tests, you will score 95%. To get the extra 5% you need to do the “extra credit” requirement as noted above. <strong>Implementation details</strong>

You *must* use a vector<em>&lt;</em>unsigned char **<em>&gt; </em>to store your image slices (see class outline below). When building the vector, you can create space for each slice as you read the .raw data and use push back() to push it onto the back of the vector. You can also set the vector to be the right number of elements and used the usual array index [] operator to set each element as required.

You *must* use dynamic memory allocation (using the “new” operator) to allocate space for each slice, so that you can write a statement like “slices[sliceIndex][row][col]” to access

Figure 1: Memory allocation layout for the volimage data structure

the appropriate 3D intensity value in your later code. You must also ensure you can correctly delete all the memory you allocated (deletion occurs in your destructor). See figure 1 for a visualization on how this allocation should be implemented.

The following class structure is mandated (you must also user header and implementation files as expected):

<table width="567">

 <tbody>

  <tr>

   <td width="567">class VolImage{ private: <em>// private members</em>int width, height; <em>// width and height of image stack </em>std::vector&lt;unsigned char**&gt; slices; <em>// data for each slice, in order </em>public: <em>// public members</em>VolImage(); <em>// default constructor – define in .cpp</em>~VolImage(); <em>// destructor – define in .cpp file</em><em>// populate the object with images in stack and</em><em>//set member variables define in .cpp </em>bool readImages(std::string baseName);<em>// compute difference map and write out; define in .cpp </em>void diffmap(int sliceI, int sliceJ, std::string output_prefix);<em>// extract slice sliceId and write to output – define in .cpp </em>void extract(int sliceId, std::string output_prefix);<em>// number of bytes uses to store image data bytes //and pointers (ignore vector&lt;&gt; container, dims etc) </em>int volImageSize(void); <em>// define in .cpp</em>};</td>

  </tr>

 </tbody>

</table>

You should not create any non-default constructors – that will be addressed later in the course, the constructor must simply zero width and height – the vector will be created with 0 size initially.

The destructor is where you place code to delete all the memory you have dynamically allocated (with new). If you do not do this correctly, your application will ”leak” memory. Which is bad.

For your convenience we’ve attached a command line RAW file viewer which should be invoked as follows:

python viewer.py &lt;raw filename&gt; &lt;width&gt; &lt;height&gt;

You can also use tools such as “Gimp” to view the images – this assume a row-major 2D array of bytes as you can tell it the width and height for an “indexed image”.

<ol>

 <li>You can use stringstreams for many parsing tasks: if you need to extract or convertdata to/from strings. Have a look at the examples in the notes and slides.</li>

 <li>command line parsing can be done using third party parsers – <strong>do not do this, write your own code</strong>. The order of arguments is clearly specified, you can use a for loop with argc to visit each parameter and decide if it’s the base name or a switch (like -x) or an argument for a switch. Remember that argv[i] is not a string object, it’s a C-string. You can turn it into a string as follows: string s = string(argv[i]); you can then write code like: if (s == ”-x”) do something …</li>

 <li>You can use ifstream and ofstream to read in and write out binary data in C++. Using C-style fread/fwrite is not allowed. You can read more on this at <a href="http://www.cplusplus.com/doc/tutorial/files/">http:</a></li>

</ol>

<a href="http://www.cplusplus.com/doc/tutorial/files/">//www.cplusplus.com/doc/tutorial/files/</a>