<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>NP Assignment 2 - UDP Binary Protocol</title>
</head>
<body>



<h2>Overview</h2>
<p>
This project implements a <b>UDP-based binary communication protocol</b> between a client and server.
The goal is to handle structured binary messages, simulate unreliable networks, and implement
fault tolerance using retransmissions and timeouts.
</p>

<hr/>

<h2>Key Concepts</h2>
<ul>
  <li>UDP socket programming</li>
  <li>Binary protocol handling (struct packing/unpacking)</li>
  <li>Network byte order conversion</li>
  <li>Timeout + retransmission mechanism</li>
  <li>Multi-client server handling</li>
</ul>

<hr/>

<h2>Project Structure</h2>
<pre>
np_assignment2/
│
├── client/              # UDP client implementation
├── server/              # UDP server implementation
├── protocol.h           # Binary protocol definitions
├── calcLib.c / .h       # Utility library for calculations
├── Makefile             # Build instructions
</pre>

<hr/>

<h2>Protocol Summary</h2>

<h3>1. Client → Server (calcMessage)</h3>
<ul>
  <li>type = 22</li>
  <li>message = 0</li>
  <li>protocol = 17</li>
  <li>major_version = 1</li>
  <li>minor_version = 0</li>
</ul>

<p><b>Note:</b> All fields must be converted to <code>network byte order</code> before sending.</p>

<hr/>

<h3>2. Server → Client (calcProtocol)</h3>
<p>
Contains an arithmetic assignment including:
</p>
<ul>
  <li>operation type (arith)</li>
  <li>input values (int32 / double)</li>
  <li>result field to be computed by client</li>
  <li>unique ID for tracking</li>
</ul>

<hr/>

<h3>3. Server Response</h3>
<ul>
  <li><b>OK</b> → correct result</li>
  <li><b>NOT OK</b> → incorrect result or invalid protocol</li>
</ul>

<hr/>

<h2>Client Behavior</h2>
<ul>
  <li>Send initial <code>calcMessage</code></li>
  <li>Wait for response (timeout = 2 seconds)</li>
  <li>Retransmit up to <b>2 times</b> (total 3 attempts)</li>
  <li>Abort if no response after final attempt</li>
  <li>If server sends invalid message → terminate with error</li>
</ul>

<hr/>

<h2>Server Behavior</h2>
<ul>
  <li>Accepts IP:PORT as argument (supports DNS, IPv4, IPv6)</li>
  <li>Handles multiple clients concurrently</li>
  <li>Supports only protocol version 1.0</li>
  <li>Tracks client: IP, port, ID, assignment</li>
  <li>Removes inactive jobs after 10 seconds</li>
  <li>Rejects invalid or unknown client responses</li>
</ul>

<hr/>

<h2>Build Instructions</h2>
<pre>
make        # builds client and server
make clean  # removes binaries and object files
</pre>

<hr/>

<h2>Run Instructions</h2>

<h3>Start Server</h3>
<pre>
./server 0.0.0.0:5000
</pre>

<h3>Start Client</h3>
<pre>
./client 127.0.0.1:5000
</pre>

<hr/>

<h2>Expected Output</h2>

<h3>Client Output (normal mode)</h3>
<pre>
Host 127.0.0.1, and port 5000.
ASSIGNMENT: add 1 2
OK (myresult=3)
</pre>

<h3>Client Output (debug mode)</h3>
<pre>
Connected to 127.0.0.1:5000 local 0.0.0.0:33419
ASSIGNMENT: add 1 2
Calculated the result to 3
OK (myresult=3)
</pre>

<h3>Error Cases</h3>
<ul>
  <li>Wrong protocol → "NOT OK" then exit</li>
  <li>Wrong message size → "ERROR WRONG SIZE OR INCORRECT PROTOCOL"</li>
  <li>No server response after retries → terminate</li>
</ul>

<hr/>

<h2>Important Notes</h2>
<ul>
  <li>Always use network byte order for binary fields</li>
  <li>Do not exceed MTU to avoid fragmentation issues</li>
  <li>Ensure proper timeout handling (2s × 3 retries)</li>
  <li>Server must log startup and each received message</li>
</ul>

<hr/>


<h2>Author Notes</h2>
<p>
This implementation focuses on robustness in unreliable UDP communication,
correct binary protocol handling, and clean modular C/C++ design.
</p>

</body>
</html>
