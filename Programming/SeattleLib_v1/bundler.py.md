 = bundler.py =
----
Have you ever wanted to upload and manage Seattle programs that include multiple files?   The bundler utility makes this easy.   

bundler.py provides an utility for packaging multiple files into a single file, much like a self-extracting zipfile.  This document describes usage via the **command line**.  For details on how to use it via the Repy API, go [wiki:SeattleLib/bundle.repy here].

bundler.py is designed with the intention to allow repy programmers to easily upload static files along with their repy programs.  You can write your program assuming that these files will be present in the local directory, bundle the said files into the program and then distribute it.  The extraction of these files is done for you, before execution.

Bundles are identified by a **.bundle.repy** extension.





 == Quickstart Guide ==
----
To create a bundle **myprog.bundle.repy**, containing a program **the files **a.txt**, **bfile** and **cdat** within the program **myprog.repy''', do the following:

```sh
# Create a bundle myprog.bundle.repy containing the contents of myprog.repy.
python bundler.py create myprog.repy myprog.bundle.repy
# List their filenames (They cannot contain spaces) in the same line.
python bundler.py add myprog.bundle.repy a.txt bfile cdat
```

You now have an auto-extracting bundle, **myprog.bundle.repy**.   You can upload and use this via seash as follows:

```sh
# This uploads and runs the bundle.  
# a.txt bfile and cdat will be there when your code is executed.
username@ !> on %1 run myprog.bundle.repy
```




 == How to Use ==
----
 === Command-Line ===
----
A command-line wrapper of this class is available, and is accessed via the Python program **bundler.py**.  The functionality provided is largely identical to that provided by the API.

| Action | Syntax | Description |
| create | $ python bundler.py create source_fname output_fname | Creates a bundle at output_fname.  |
| add | $ python bundler.py add bundle_fname file1 file2 ... | Adds the specified files to the bundle. |
| extract | $ python bundler.py extract bundle_fname file1 file2 ... | Extracts the specified files from the bundle.  Existing files in the current directory with the same name(s) will be overwritten.|
| extract-all | $ python bundler.py extract-all bundle_fname | Extracts all files from the bundle.  Existing files in the current directory with the same name(s) will be overwritten.|
| list | $ python bundler.py list bundle_fname | Lists the contents of the bundle. |
| remove | $ python bundler.py remove bundle_fname file1 file2 ... | Removes the specified files from the bundle.  Note: This data cannot be recovered!|
| wipe | $ python bundler.py wipe source_fname output_fname | Removes all traces of a bundle from a file.  A useful side effect is that it removes all bundled files from the bundle, as well as restoring the bundle to the file that was originally used to create the bundle.  |




 == Command-Line Usage Example: Creating a Self-Extracting HTTP Server Bundle ==
----
We have a repy program **httpserver.repy**, and it depends on the files **logo.png** and **index.html**. We would like to bundle the files alongside **httpserver.repy** into an auto-extracting repy program, **httpserver.bundle.repy**.  **error.log** is a file that we do NOT want included.

Assume that we have the following files in the current directory:
 * httpserver.repy - This is our simple http server that serves the files **logo.png** and **index.html**.
 * logo.png
 * index.html
 * error.log


### Creating a Bundle
----
First, we must create a bundle that will contain code from **httpserver.repy**.
```sh
$ python bundler.py create httpserver.repy server.bundle.repy
```


### Adding Files to a Bundle
----
Now, let's add the files that we want to embed into the bundle.  The files here are **logo.png** and **index.html**.  Since we can make mistakes, we'll also include **error.log** on purpose.
```sh
$ python bundler.py all server.bundle.repy logo.png index.html error.log
```




### Removing Elements from a Bundle
----
It seems like we've accidentally included **error.log**!  Thankfully, we don't have to start afresh; we can simply remove the extraneous file.
```sh
$ python bundler.py remove server.bundle.repy error.log
```



### Checking a Bundle's Contents
----
Before we finalize the auto-extracting bundle, it would be wise to make sure that the bundle contains all the files that we want it to have.  We need two files, **logo.png** and **index.html**.  The listed file sizes are their sizes within the bundle.
```sh
$ python bundler.py list server.bundle.repy
```
And we should get the following output (filesizes are simulated, actual values depend on the size of your files):
```
Bundle contents:
Filename   Size (bytes)
logo.png   23536
index.html 1338
```

Congratulations, you now have an auto-extracting bundle named httpserver.bundle.repy!



### Extracting Files from a Bundle
----
Even though the most useful feature of the bundler is its self-extraction feature, you can also extract files from the bundle manually.  You can do it in two ways:

You can do it on a file-by-file basis:
```sh
$ python bundler.py extract httpserver.bundle.repy logo.png index.html
```

Or you can tell the bundler to extract everything simultaneously:
```sh
$ python bundler.py extract-all httpserver.bundle.repy
```



### Clearing a Bundle / Source File Recovery
----
However, now suppose you need access to the original httpserver code.  However, you only have access to the bundle, as a colleague of yours never bothered to include a version of the original source code.  You can use the clear function to remove all traces of a bundle from a file.
```sh
$ python bundler.py wipe httpserver.bundle.repy original_httpserver.repy
```