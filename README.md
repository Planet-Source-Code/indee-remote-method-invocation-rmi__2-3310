<div align="center">

## Remote Method Invocation \(RMI\)


</div>

### Description

Hi! guys this tutorial will give you the basic idea about "What RMI stands for and how does it work.Let's start it.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Indee](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/indee.md)
**Level**          |Intermediate
**User Rating**    |4.3 (34 globes from 8 users)
**Compatibility**  |Java \(JDK 1\.1\), Java \(JDK 1\.2\)
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__2-57.md)
**World**          |[Java](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/java.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/indee-remote-method-invocation-rmi__2-3310/archive/master.zip)





### Source Code

<p>The Remote Method Invocation (RMI) API is a core component of the Java programming
 language, and adds the capability to develop fully distributed applications.
 i.e. an application that will execute across multiple host systems. Since Java
 is designed to be a language for the Internet, it must include support for communication
 between programs on different machines. RMI enables objects executing on one
 host to invoke methods of objects on remote hosts to assist them in performing
 their tasks. These methods execute on the remote hosts, and this is where the
 term remote method invocation stems from.</p>
<p>So what exactly is RMI? RMI is a term used to describe not
only the act of invoking a method defined in a different address
space, but also the changes made to the Java language to support
remote method calls. These changes included new tools, APIs and
run-time support. RMI enables the methods of an object in one
virtual machine to call the method of an object in another
virtual machine with the same syntax and ease as a local machine
invocation.</p>
<p>There are a number of approaches that have been taken when
developing a distributed system, e.g. the Internet is an example
of a distributed system that makes use of TCP/IP in a
client/server fashion. There are three main approaches to
tackling a distributed system within Java, all have their pros
and cons:</p>
<ul>
<li><b>Distributed Component Object Model (DCOM)</b><br />
<br />
DCOM uses remote procedure calls
that invoke the methods of objects running on other systems. It
is an excellent framework upon which to develop a distributed
object-based system. However, it has a heavy bias towards legacy
Windows applications, and as a result cannot provide a 100% pure
Java solution.<br />
<br />
</li>
<li><b>Common Object Request Broker Architecture
(CORBA)</b><br />
<br />
CORBA is a framework that provides
support for the development of distributed systems in the form of
a language-independent model. Although this would appear to be a
big advantage in the development of a distributed system, when it
comes to Java we can consider it a disadvantage as it forces Java
applications to use an external object model. On the plus side to
using CORBA, it allows a variety of programming languages to
interact.<br />
<br />
</li>
<li><b>Remote Method Invocation (RMI)</b><br />
<br />
RMI is the Java way to approaching
the development of a distributed system. Its main advantage is
that it is closely integrated into the Java object model as an
API. However, it is limited in use to pure Java applications
only. If you are developing a distributed system that contains
objects written in other languages, then CORBA is the best
solution to use.</li>
</ul>
<p>There are a few terms that you might come across when
discussing the RMI API, most of which relate to the concept that
RMI is built upon- that of local and remote objects.</p>
<ul>
<li><b>Local objects</b> - objects that execute on the host
machine</li>
<li><b>Remote objects</b> - objects that execute on all other
hosts</li>
<li><b>Exported</b> - objects on remote hosts are exported,
allowing remote invocation</li>
<li><b>Remote registry server</b> - an object exports itself by
registering with the remote registry server. It also assists
objects on other hosts to remotely access its registered objects
using a database of names that are associated with objects.</li>
<li><b>Client object</b> - any local object that invokes a remote
objects method (also known simply as a client).</li>
<li><b>Server object</b> - any remote object whose methods may be
invoked by a local object (also known simply as a server).</li>
<li><b>Stub</b> - a local object that acts as a local proxy for
the remote object, providing the same methods as the remote
object.</li>
<li><b>Skeleton</b> - a proxy for the remote object that is
situated on the same host as the remote object. The stub on the
local host and skeleton then communicate with each other via a
remote reference layer.</li>
</ul>
<p>Probably the best way to get a better idea of what is actually
happening here is to look at an example!.</p>
<p>Follow the following steps</p>
  <ol>
  <li>First copy all the files for this exercise into a directory of your
   choice. The required files are:<br />
   <br />
   CalculatorService.java<br />
		 <pre>
		 import java.rmi.*;
import java.rmi.Naming;
import java.io.*;
//
//
// CalculatorClient
//
//
public class CalculatorClient
{
	public static void main(String args[]) throws Exception
	{
		// Check for hostname argument
		if (args.length != 1)
		{
			System.out.println ("Syntax - CalculatorClient host");
			System.exit(1);
		}
		// Assign secuirty manager
		if (System.getSecurityManager() == null)
		{
			System.setSecurityManager(new RMISecurityManager());
		}
		// Call registry for PowerService
		CalculatorService service = (CalculatorService) Naming.lookup
			("rmi://" + args[0] + "/CalculatorService");
		DataInputStream din = new DataInputStream (System.in);
		for (;;)
		{
			System.out.println ("1 - Calculate square");
			System.out.println ("2 - Calculate power");
			System.out.println ("3 - Exit"); System.out.println();
			System.out.print ("Choice : ");
			String line = din.readLine();
			Integer choice = new Integer(line);
			int value = choice.intValue();
			switch (value)
			{
				case 1:
					System.out.print ("Number : ");
					line = din.readLine();System.out.println();
					choice = new Integer (line);
					value = choice.intValue();
					// Call remote method
					System.out.println ("Answer : " + service.square(value));
					break;
				case 2:
					System.out.print ("Number : ");
					line = din.readLine();
					choice = new Integer (line);
					value = choice.intValue();
					System.out.print ("Power : ");
					line = din.readLine();
					choice = new Integer (line);
					int power = choice.intValue();
					// Call remote method
					System.out.println ("Answer : " + service.power(value, power));
					break;
				case 3:
					System.exit(0);
				default :
					System.out.println ("Invalid option");
					break;
			}
		}
	}
}
		 </pre>
   CalculatorServer.java<br />
		 <pre>
		 import java.math.*;
import java.rmi.*;
import java.rmi.server.*;
//
//
// CalculatorServer
//
// Server for a RMI service that calculates
//
public class CalculatorServer extends UnicastRemoteObject implements CalculatorService
{
	public CalculatorServer () throws RemoteException
	{
		super();
	}
	// Calculate the square of a number
	public BigInteger square ( int number ) throws RemoteException
	{
		String numrep = String.valueOf(number);
		BigInteger bi = new BigInteger (numrep);
		// Square the number
		bi = bi.multiply(bi);
		return (bi);
	}
	// Calculate the power of a number
	public BigInteger power ( int num1, int num2) throws RemoteException
	{
		String numrep = String.valueOf(num1);
		BigInteger bi = new BigInteger (numrep);
		bi = bi.pow(num2);
		return (bi);
	}
	public static void main ( String args[] ) throws Exception
	{
		// Assign a security manager, in the event that dynamic classes are loaded
		if (System.getSecurityManager() == null)
			System.setSecurityManager ( new RMISecurityManager() );
		// Create an instance of our power service server ...
		CalculatorServer svr = new CalculatorServer();
		// ... and bind it with the rmi registry
		Naming.bind ("CalculatorService", svr);
		System.out.println ("Service bound....");
	}
}
		 </pre>
   CalculatorClient.java<br />
		 <pre>
		 import java.math.BigInteger;
import java.rmi.*;
//
//
// CalculatorService Interface
//
// Interface for a RMI service that calculates powers
//
public interface CalculatorService extends java.rmi.Remote
{
	// Calculate the square of a number
	public BigInteger square ( int number ) throws RemoteException;
	// Calculate the power of a number
	public BigInteger power ( int num1, int num2) throws RemoteException;
}
		 </pre>
   java.policy<br />
  </li>
  </ol>
<blockquote>
 <p>for java.policy file, type the following text in notepad and save as "java.policy"</p>
 <pre>
grant
{
  permission java.net.SocketPermission "*:1024-65535",
   "connect,accept,resolve";
  permission java.net.SocketPermission "*:1-1023",
   "connect,resolve";
};
</pre>
</blockquote>
<ol>
 <li ><br />
 <br />
 </li>
 <li>Now compile all the .java files by isuuing the following command:<br />
 <br />
 >javac *.java</li>
 <li ><br />
 <br />
 </li>
 <li>Next create the stubs and skeletons required by the program. The stubs and
 skeletons communicate through a remote reference. This layer enables communication
 via a transport protocol. Currently RMI uses TCP.<br />
 <br />
 >rmic CalculatorServer<br />
 <br />
 Note that no extension is specified as rmic takes a .class file as its default.
 This will produce the following files in the current directory:<br />
 <br />
 CalculatorClient.java<br />
 CalculatorServer.java<br />
 CalculatorService.java<br />
 java.policy<br />
 CalculatorService.class<br />
 CalculatorClient.class<br />
 CalculatorServer.class<br />
 CalculatorServer_Stub.class<br />
 CalculatorServer_Skel.class<br />
 <p>As you can see, the _Stub and _Skel.class have been automatically created.</p>
 </li>
 <li>For RMI to work we will need to start a program that acts as a remote registry
 server. This is simply a program that listens on the default port 1099 for
 incoming requests to access named objects. The named objects must register
 themselves with the remote registry program in order to be made available
 to requesters. Type the following command to start up the remote registry
 server:<br />
 <br />
 >start rmiregistry
 <p>This will initiate another DOS window, which must be left open while this
  exercise is running. Minimise it if you wish.</p>
 </li>
 <li>Now all we need is to actually execute the server and client programs. However,
 owing to the security restrictions imposed by Java 2, you will need to issue
 the following commands.<br />
 <br />
 <p>Execute the server from your current command prompt:</p>
 >java -Djava.security.policy=java.policy CalculatorServer
 <p>This will then report a “Service bound…” message –
  your server is up and running!</p>
 <p>Now do the same with the client, but this time start up another DOS window
  and change into the directory where you invoked the previous command. Here’s
  the command:</p>
 >java -Djava.security.policy=java.policy CalculatorClient localhost
 <p>You will now see something that looks like this:</p>
 <pre>
1 - Calculate square
2 - Calculate power
3 - Exit
Choice :
</pre>
 <p>Experiment with this program.</p>
 </li>
</ol>
<p>So what is actually happening here? The file
CalculatorService.java is our interface- in other words, a method
that contains abstract methods. Another class must implement
these methods, and this is exactly what RMI is all about.
Here’s the code for CalculatorService.java:</p>
<pre>
import java.math.BigInteger;
import java.rmi.*;
//
//
// CalculatorService Interface
//
// Interface for a RMI service that calculates powers
//
public interface CalculatorService extends java.rmi.Remote
{
 // Calculate the square of a number
 public BigInteger square ( int number ) throws RemoteException;
 // Calculate the power of a number
 public BigInteger power ( int num1, int num2) throws RemoteException;
}
</pre>
<p>Now we need to implement the interface, i.e., the actual code
of the square and power methods. The main() method will be
responsible for creating an instance of the CalculatorServer and
registering (or binding) the service with the RMIRegistry. It is
also responsible for assigning the security manager. Here is the
CalculatorServer program where the actual power and square code
is situated:</p>
<pre>
import java.math.*;
import java.rmi.*;
import java.rmi.server.*;
//
//
// CalculatorServer
//
// Server for a RMI service that calculates
//
public class CalculatorServer extends UnicastRemoteObject implements CalculatorService
{
 public CalculatorServer () throws RemoteException
 {
 super();
 }
 // Calculate the square of a number
 public BigInteger square ( int number ) throws RemoteException
 {
 String numrep = String.valueOf(number);
 BigInteger bi = new BigInteger (numrep);
 // Square the number
 bi = bi.multiply(bi);
 return (bi);
 }
 // Calculate the power of a number
 public BigInteger power ( int num1, int num2) throws RemoteException
 {
 String numrep = String.valueOf(num1);
 BigInteger bi = new BigInteger (numrep);
 bi = bi.pow(num2);
 return (bi);
 }
 public static void main ( String args[] ) throws Exception
 {
 // Assign a security manager, in the event that dynamic classes are loaded
 if (System.getSecurityManager() == null)
  System.setSecurityManager ( new RMISecurityManager() );
 // Create an instance of our power service server ...
 CalculatorServer svr = new CalculatorServer();
 // ... and bind it with the rmi registry
 Naming.bind ("CalculatorService", svr);
 System.out.println ("Service bound....");
 }
}
</pre>
<p>Next we need a client that will make use of the service we
have created. The client simply calls the registry to obtain a
reference to the remote object and call the available methods.
Any underlying communication is hidden.</p>
<p>The line</p>
CalculatorService service = (CalculatorService) Naming.lookup
("rmi://" + args[0] + "/CalculatorService");
<p>instantiates a new instance of CalculatorService which is
created by a call to the registry, and to identify the service we
specify an RMI URL. This URL is the hostname on which the service
is located, in this case “localhost” and the logical
name of the service. The returned object can then be treated just
like any other local object:</p>
System.out.println("Answer : " + service.square(value));
System.out.println("Answer : " + service.power(value, power));
<p>Finally, here is the full source code for
CalculatorClient:</p>
<pre>
import java.rmi.*;
import java.rmi.Naming;
import java.io.*;
//
//
// CalculatorClient
//
//
public class CalculatorClient
{
 public static void main(String args[]) throws Exception
 {
 // Check for hostname argument
 if (args.length != 1)
 {
  System.out.println ("Syntax - CalculatorClient host");
  System.exit(1);
 }
 // Assign secuirty manager
 if (System.getSecurityManager() == null)
 {
  System.setSecurityManager(new RMISecurityManager());
 }
 // Call registry for PowerService
 CalculatorService service = (CalculatorService) Naming.lookup
  ("rmi://" + args[0] + "/CalculatorService");
 DataInputStream din = new DataInputStream (System.in);
 for (;;)
 {
  System.out.println ("1 - Calculate square");
  System.out.println ("2 - Calculate power");
  System.out.println ("3 - Exit"); System.out.println();
  System.out.print ("Choice : ");
  String line = din.readLine();
  Integer choice = new Integer(line);
  int value = choice.intValue();
  switch (value)
  {
  case 1:
   System.out.print ("Number : ");
   line = din.readLine();System.out.println();
   choice = new Integer (line);
   value = choice.intValue();
   // Call remote method
   System.out.println ("Answer : " + service.square(value));
   break;
  case 2:
   System.out.print ("Number : ");
   line = din.readLine();
   choice = new Integer (line);
   value = choice.intValue();
   System.out.print ("Power : ");
   line = din.readLine();
   choice = new Integer (line);
   int power = choice.intValue();
   // Call remote method
   System.out.println ("Answer : " + service.power(value, power));
   break;
  case 3:
   System.exit(0);
  default :
   System.out.println ("Invalid option");
   break;
  }
 }
 }
}
</pre>
  <p>ok that's it. I hope you got a fair idea about the RMI. For any clarification
  on this tutorial please feel free to ask question in our java forum. </p>

