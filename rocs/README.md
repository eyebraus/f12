
Rochester Server (Rocs)
===============

Introduction
========

Objectives
---------

The Rocs server is a mimimal web server written in Ruby.  It supports
the basic functionality of a client-server procedure call.  The client
is a web browser submitting a URL or a form.  The Rocs server provides
the TCP, HTTP, and CGI processing.  Finally, a server script is
called, which returns/replies with a web page to go through the Rocs
server to back to the browser.

Rocs is simple.  Each component is at most tens of lines.  It is not
designed to be efficient or safe (against attacks).

The simplicity has two purposes.  The first is as a class project.
Students can work together.  Multiple students may develop the same
component (and test cases) and them discuss and merge the code to
create a master version.  The components can be adjusted and linked
together during one or more code camps.

The second is portable web application development.  Many of us find
convenient to program and debug a web application on a laptop
computer, but installing a full web server is too onerous a task.  One
has to install all dependent components and keep them up to date with
every system/machine change/upgrade.  The Rocs server would be just a
few hundred lines of Ruby code and can run on any machine that has a
Ruby installation.  There are other uses such as GUI on local host
with no network connection, e.g. hg serve, and a portable GUI,
i.e. usable on any machine that has a browser and can run Ruby.

Many open-source web servers exist.  They are often too complex to be
intimidating.  The Rocs server would mimic these complex servers by
including the bare essentials and the fundamental structure.  Students
will be able to appreciate the more complex designs and experiment
with their ideas in "beefing" up the simple server and extend in
interesting directions.  The two goals of the project, teaching web
server design and developing a useful tool, are two sides of a single
effort.

Conceptual design
---------------

It is helpful to simply the simple server to use more familiar
concepts.  The basic concept is a procedure call.  There is the
invocation that has the function name and a list of parameters.
Consider the list as a hash table with name-value pairs.  The function
call returns a block of text.

The function call is distributed in that the caller may reside on a
different machine.  The familiar concept is the distributed Ruby
(drb).  In drb, a remote server is created and then a local client.
The underlying implementation is RPC (remote procedural call).

In drb, we have the three components: Ruby <-> RPC <-> Ruby.  In Rocs
server, we have Browser <-> TCP <-> HTTP <-> CGI <-> Ruby.  The
Browser makes a TCP connection.  The server interprets the incoming
text as HTTP and CGI (common gateway interface).  The Rocs server will
support only the basic features of HTTP and CGI.

Use in WeBWork
------------

We will test the new web server by using it for WeBWork, the widely
used online homework delivery system originated at University of
Rochester, led by Michael Gage.  Mike will lead a parallel project to
create server scripts to be called by the new web server.

Github repository
--------------

Create your free github account if you haven't done so.  

Go to the project repositories, https://github.com/cs253/f12 for the web
server and https://github.com/mgage/standalone-question-renderer for
the WeBWork server.

Click 'fork' to create a branch in your github.

Once you have completed a draft implementation and ready for code
review, create a 'pull request'.  Mike and I will give you feedback and
may ask for changes before merging your files into the root repositories.

Time line
-------

We will start assigning the components to students on Tuesday Dec. 4.
Pick a component to work on.  Complete the design first.  Ask for
comments through a pull request by Friday evening.  Complete the
implementation.  Ask for comments by Tuesday evening.

Then we will put the complete system together by sitting together in a
coding camp.  Lunch will be provided.  I have reserved the lecture
room for two afternoons Thursday Dec. 13 and Friday afternoon 12pm to
5pm.  We shouldn't need 10 hours.  The reservation is an upperbound.

The attendance at the coding camp is not required.  If you cannot
attend, choose the type of work that would help others without you
being there, e.g. writing test cases.

Components
========

HttpServer
---------

calss HttpServer
  def process( request )
     # invoke the script in cgi_bin_path
     # pass in the parameters as a hashtable
     # return the results of the script
  end

  def start
     tcp = TcpServer.new( self )
     tcp.start
  end
end

server = HttpServer.new { |s|
  s.host = "localhost"
  s.port = 8000
  s.cgi_bin_path = "galaxy/faraway"
}

server.start

TcpServer
--------

class TcpServer
   def initialize( http )
     @http = http
   end

   def start 
     # for each socket receive
     # parse the text using HttpRequest
     # pass the request to http
     ...
     @http.process( request )
     # send the results back
   end
end

Use Ruby 'socket' package.   

HttpRequest
----------

Parse the input text and create an object with path and a hashtable
containing all key-value pairs.

Config
-----

Environment variables and values such as the CGI bin path, stored and retrieved from a file on disk.

Logger
------

Support Logger.warn, Logger.error, and Logger.info calls.  Keep logs in a file on disk.

Echo server
---------

List the received information on a web page and send it back.  Useful for debugging.

Assume that the input is a hash table in Json format and being piped into the script.

Use 'cgi' to help creating a web page.  See the link in the Resources below.


Resources
=======

web programming using Ruby CGI
---------------------------

You can create web page texts much easier using the 'cgi' module.  It
provides translation to wire formats, e.g. '/' to %2F, using
CGI.escape and CGI.escapeHTML methods.  It helps to create HTML forms
and pages with cookies and sessions.  See

http://www.ruby-doc.org/docs/ProgrammingRuby/html/web.html

webrick server toolkit
-----------------

It is developed by TAKAHASHI Masayoshi and GOTOU YUUZOU.  It supports
HTTP, CGI servelets, HTTPS, proxy, and authentication.  It is
distributed with core Ruby in lib/webrick.  A copy of the code is in
class repos [repos]/material/webrick.  The core functions of the HTTP
server are implemented in the scripts in webrick/httpserver/.

thin web server and the event machine
-------------------------------

"Thin is a Ruby web server that glues together 3 of the best Ruby libraries in web history:
  * the Mongrel parser: the root of Mongrel speed and security
  * Event Machine: a network I/O library with extremely high scalability, performance and stability
  * Rack: a minimal interface between webservers and Ruby frameworks

Which makes it, with all humility, the most secure, stable, fast and extensible Ruby web server"

A copy of code is at [repos]/material/thin-1.3.1.  The server code,
which is a shell connecting the backend, is
thin-1.3.1/lib/thin/server.rb.  The TCP processing is done by the
Event Machine.  The source code is in
[repos]/material/eventmachine-0.12.10.



