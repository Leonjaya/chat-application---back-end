# chat-application---back-end
const express = require('express');
const http = require('http');
const socketIo = require('socket.io');
const cors = require('cors');
const mongoose = require('mongoose');

const app = express();
app.use(cors());
app.use(express.json());

const server = http.createServer(app);
const io = socketIo(server, {
  cors: {
    origin: "http://localhost:3000",
    methods: ["GET", "POST"]
  }
});

mongoose.connect('mongodb://localhost/chat-app', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

io.on('connection', (socket) => {
  console.log('New user connected');
  
  socket.on('sendMessage', (message) => {
    io.emit('receiveMessage', message); // Broadcasting message to all users
  });

  socket.on('disconnect', () => {
    console.log('User disconnected');
  });
});

server.listen(5000, () => console.log('Server running on port 5000'));
const mongoose = require('mongoose');

const MessageSchema = new mongoose.Schema({
  user: String,
  message: String,
  timestamp: {
    type: Date,
    default: Date.now,
  },
});

const Message = mongoose.model('Message', MessageSchema);

module.exports = Message;
const Message = require('./models/Message');

socket.on('sendMessage', async (message) => {
  const newMessage = new Message(message);
  await newMessage.save();

  io.emit('receiveMessage', message); // Broadcasting message to all users
});
