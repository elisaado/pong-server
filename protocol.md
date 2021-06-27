# 2 player pong protocol

The server and the client use socket.io to communicate.

The communication happens using a command-shell like interface (for example `JOIN 12345`)

## Flow

We have many clients and one server. The server is responsible for sharing the data between clients, and generally facilitating communication. The server provides rooms that two clients can join.

1. Both clients connect and set a name.
2. Either the client joins a room by code (in which case the client receives the information about the room, like the dimensions of the game and the score to win), or creates a new one (in which case the client receives the code of the room, to share with the other client). In both cases, the clients receive each others' names (the names are not used in the protocol, but must be displayed to the user).
3. Both clients say they're ready.
4. Both clients receive a message from the server that confirms the game will start in 3 seconds.
5. Both clients receive a message from the server that the game starts now.

Steps 6. through 9. happen in a loop (with step 7 and 8 happening non-synchronously), until the score threshold is met.

6. The ball begins moving in a random direction and the server sends the speed vectors of the ball, and its position
7. The server sends the new ball speed vectors and position on every bounce.
8. Every time the paddle moves, the client informs the server of this movement, the server, in turn, informs the other client of this.
9. Whenever the paddle does not hit the ball, a point is added to the score for that user. And a message is sent to both clients to increment the score.

When the score threshold is met, step 10 is executed.

10. Both clients receive a messsage that the game has stopped, and that either client one, or client two, has won.
11. END

## Commands

Commands are sent from the client, to the server.

### SETNAME

#### Description

Sets a name

#### Arguments

- The name

#### Example

`SETNAME Eli Saado`

### JOIN

#### Description

Makes the client join a room by code

#### Arguments

- The room number

#### Example

`JOIN 12345`

### READY

#### Description

Tells the server and other client you are ready.

#### Arguments

None

#### Example

`READY`

### MOVE

#### Description

Tells the server and other client to move your paddle.

#### Arguments

- The y value (vertical position) of the paddle (0 being top, the height sent with the room info being the bottom.)

#### Example

`MOVE 200`

## Messages

Messages are sent from the server, to the client.

### ROOMINFO

#### Description

Tells the client information about the room

#### Arguments

- score threshold
- amount of clients in the room (so 1 or 2)
- width of the playing field
- height of the playing field

#### Example

`ROOMINFO 8 2 800 600`

### NAME

#### Description

Tells the client the other client's name

#### Arguments

- The name

#### Example

`NAME Wouter.db`

### STARTING

#### Description

Tells both clients the game will start in 3 seconds.

#### Example

`STARTING`

### STARTED

#### Description

Tells both clients the game has started (starts now).

#### Example

`STARTED`

### MOVED

#### Description

Tells the client the other client's paddle position

#### Arguments

- The y value (vertical position) of the paddle (0 being top, the height sent with the room info being the bottom.)

#### Example

`MOVED 20`

### BALLSPEED

#### Description

Tells both clients the x and y speed of the ball

#### Arguments

- The x value (horizontal speed vector) of the ball, relative to its current position.

- The y value (vertical speed vector) of the ball, relative to its current position.

#### Example

`BALLSPEED -2 3`

### BALLPOS

#### Description

Tells both clients the x and y position of the ball

#### Arguments

- The x value (horizontal position) of the ball
- The y value (vertical position) of the ball

### SCORE

#### Description

Tells both clients the score

#### Arguments

- The score of the room creator
- The score of the room joiner

#### Example

`SCORE 7 4`

### WINNER

#### Description

Tells both clients who won

#### Arguments

- The socket.io ID of the client

#### Example

`WINNER x8WIv7-mJelg7on_ALbx`
