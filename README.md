# SimpleNet
An easy-to-use, event-driven, asynchronous, network application framework compiled with Java 10.

# How can I start using SimpleNet?
 1. Because SimpleNet is compiled with Java 10, you'll first need to build it into a dependency (coming to Maven soon) and add it to your build path.
 2. Next, you must edit your `module-info.java` to include the following:

```java
requires SimpleNet;
```
 3. To create a `Client`, you can use the following:
```java
// Instantiate a new Client.
var client = new Client();

// Attempt to connect to a server.
client.connect("localhost", 43594);

// Register one connection listener.
client.onConnect(() -> {
    System.out.println(client + " has connected to the server!");
    
    // Builds a packet and sends it to the server immediately.
    Packet.builder().putByte(1).putInt(42).writeAndFlush(client);
});

// Register one disconnection listener.
client.onDisconnect(() -> System.out.println(client + " has disconnected from the server!"));
```

 4. To create a `Server`, you can use the following:

```java
// Instantiate a new Server.
var server = new Server();

// Bind the server to an address and port.
server.bind("localhost", 43594);

// Register one connection listener.
server.onConnect(client -> {
    System.out.println(client + " has connected!");

   /*
    * When one byte arrives from the client, switch on it.
    * If the byte equals 1, then "request" an int and
    * print it when it arrives.
    *
    * Because `readByteAlways` is used, the server will always
    * attempt to read one byte.
    */
    client.readByteAlways(b -> {
        switch (b) {
            case 1:
                client.readInt(System.out::println);
        }
    });
});

// Register one disconnection listener.
server.onDisconnect(client -> System.out.println(client + " has disconnected!"));
```

 5. Congratulations, you're finished! Be sure to run the `Server` first, as every `Client` will not be able to connect otherwise.
