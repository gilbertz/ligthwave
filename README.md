# ligthwave
PhysioNetWorks open source light wave

Lightwave delopy ducoment
The lightwave server, lightwave, run as a cgi application within a web server. The web server collects user requests (typically made using the lightwave client running in a web browser on another computer) and forwards them to the lightwave  server. The lightwave server parses the requests, obtain the requested data from local storage from a data repository such as physionet, construct appropriate responses, and passed them back to the web server. 
Download the LightWAVE sources
The LightWAVE sources are available within the LightWAVE project on PhysioNetWorks. To obtain them:
1.	If you have not already done so, go to https://physionet.org/users/ and create a personal PhysioNetWorks account. It's free and the process takes only a minute or two.
2.	Log in to your PhysioNetWorks account and look for the link to LightWAVE in the Projects section on your home page. Follow the link and look for the signup message ("Interested PhysioNetWorks members may obtain access to this project now by clicking here."). Click on the word "here" in the message to join the project.
3.	From the project's archives ( https://physionet.org/works/LightWAVE/files), find and download the most recent set of sources. Each version is available as a tarball (e.g., lightwave-0.43.tar.gz) that can be downloaded as a single file and unpacked, or you can download individual files from the most recent version from the lightwave subdirectory. For downloading hints, see How can I download binary files?; for instructions on unpacking tarballs, see How can I unpack a .tar.gz archive (a "tarball")?. If you choose to download individual files, note that you will need to create a complete copy of the lightwave directory tree in order to test your installation with the LightWAVE client.
Edit the Makefile
In the lightwave directory, open Makefile in a text editor (not a word processor) and read it. The comments near the top of the file describe other free software components you may need to install before compiling and installing the LightWAVE server.
Currently, these components include:
•	a properly configured web server, such as Apache
•	libcgi
•	libwfdb
•	libcurl
•	an ANSI/ISO C compiler, such as gcc and a few other standard POSIX tools including 'make', 'cp', 'mkdir', 'mv', 'rm', 'sed', and 'tar' (all standard on Linux and Mac OS X, components of Cygwin on Windows)
•	perl and CGI.pm (for lw-scribe only)
(This list may be incomplete; consult the Makefile for any additions.)
As the comments near the top of the file note, you may need to change the values of some parameters that are defined in Makefile to match your installation.
Build and install the LightWAVE server
Once you have checked the Makefile and made any necessary changes to it, open a terminal window, go to the directory that contains Makefile, and run the command
    make
The output of this command should look something like this:
    gcc -g -DLWDIR=\"/home/physionet/html/lightwave\" -DLWVER=\"0.43\" -DLW_WFDB=\""/usr/local/database http://physionet.org/physiobank/database"\" server/lightwave.c -o lightwave -lcgi -lwfdb
    mkdir -p /home/physionet/cgi-bin
    cp -p lightwave /home/physionet/cgi-bin
    rm -f lightwave *~ */*~ */*/*~
    mkdir -p /home/physionet/html/lightwave
    mv client/lightwave.html .
    cp -pr client/* /home/physionet/html/lightwave
    sed s+http://physionet.org/cgi-bin/+/cgi-bin/+ \
     <client/js/lightwave.js >/home/physionet/html/lightwave/js/lightwave.js
    sed "s/\[local\]/0.43/" <lightwave.html >/home/physionet/html/lightwave/index.html
    mv lightwave.html client

    LightWAVE has been installed.  If an HTTP server is running,
    use LightWAVE by opening your web browser and visiting
        http://HOST/lightwave/
    (replacing HOST by the hostname of this server, or by localhost
    or 127.0.0.1 to run without a network connection).
Test the installed server
From the lightwave directory, run this command:
    make test
The output of this command should look something like this:
    The LightWAVE server appears to be running properly in command-line mode.
    Test it with the LightWAVE client by pointing your web browser to:
        http://localhost/lightwave/
If the test with the LightWAVE client doesn't work at all, check that Apache (or whatever web server you installed) is running.
If the LightWAVE client loads, but (after about 10 seconds) displays the message:
    The LightWAVE server at
    http://physionet.org/cgi-bin/lightwave
    is not responding properly.  Please check
    the network connection.  Select another server
    on the Settings tab if necessary.
and the suggestions in the message are not sufficient to solve the problem, check that:
•	lightwave (the compiled LightWAVE server) is installed in a directory that is named as a directory containing CGI applications in the appropriate Apache configuration file
•	lightwave has appropriate ownership and permissions so it can be run by Apache
•	any shared libraries (DLLs) needed by lightwave, including libcgi, libwfdb, and libcurl, are in locations where Apache can find them, and they have appropriate ownership and permissions to be read by Apache
Using your locally hosted server
If you have a network connection, you may use your server by connecting to it with a browser and LightWAVE client running on the same host. The server will use PhysioBank as its data repository by default. (This is the configuration that you tested in the previous section.)
If you have set up your own data repository (see below) on the same host as the server, you may use LightWAVE without a network connection, by connecting to the server with a browser and LightWAVE client also running on the same host.
In order to use your server from a different host, the LightWAVE client must be configured to use it rather than PhysioNet's public server. To do this, go to the client's Settings tab and set the server URL to the location of your server. This setting will persist only until you reload the LightWAVE client. You can change the default server URL by changing the value of server just below the initial comment block in client/js/lightwave.js; this change will affect all future sessions.
Custom data repositories
The LightWAVE server, in common with all applications that use the WFDB library (libwfdb), finds data by searching a list of locations (data repositories) known as the WFDB path. The standard list of locations is determined at the time the LightWAVE server is compiled, by the value of the constant LW_WFDB. This constant is defined in Makefile as
    /usr/local/database
If you wish to use a repository other than PhysioBank:
1.	Choose a location for your repository.
o	If the repository will be in local storage, the simplest choice is to put it in /usr/local/database (or the first component of your WFDB path, whatever that is).
o	Otherwise, add the location of the repository (which can be either a location in your local file system, or a URL) to the definition of LW_WFDB in Makefile, and recompile and install the server (run "make" as above). Be sure that your repository precedes PhysioBank in the WFDB path, or it will not be searched.
2.	Make a copy of http://physionet.org/physiobank/database/DBS. Use a text editor (not a word processor) to edit it so that it includes the names of the database(s) in your repository, and put the modified DBS in the top-level directory of your repository. Important: database names may not include spaces!
3.	Make a copy of http://physionet.org/physiobank/database/wfdbcal. If your records include signal types that are not listed in it, use a text editor to add them. Put the modified wfdbcal in the top-level directory of your repository.
4.	For each database in your repository, make a subdirectory of your repository's top-level directory, with the name that you entered in DBS. Within the subdirectory:
o	Store the PhysioBank-compatible records for your database.
o	Create plain text files named RECORDS (containing a list of record names) and ANNOTATORS (containing a list of annotator names), using those in the PhysioBank databases as models.
Note that, on specific request, the LightWAVE server will read sets of annotations that are not listed in the ANNOTATORS file. The current LightWAVE client does not provide a user interface for specifying an unlisted annnotation set, however; as a result, annotation sets are not readily accessible unless their annotator names are listed in ANNOTATORS. It is harmless (though slightly inefficient) to list annotator names for non-existent annotation sets in ANNOTATORS. Make an empty ANNOTATORS file if your database is unannotated.
Notice that although your LightWAVE client lets you choose which LightWAVE server it uses, it does not let you choose which data repositories the server uses (since, by design, this is determined when the server is compiled, not at run-time). If you wish to use non-standard repositories, this can be done only by configuring your own LightWAVE server to do so.





