# @riflowsxz/baileys

A WhatsApp Multi-Device API library for Node.js that allows you to interact with WhatsApp's Web API. This is a fork of the original Baileys library with custom modifications by riflowsxz.

## Features

- **Multi-device support**: Uses WhatsApp's multi-device API
- **TypeScript support**: Full TypeScript definitions included
- **Authentication**: QR code or pair-protocol-based authentication
- **Message handling**: Send and receive various message types (text, media, buttons, lists, etc.)
- **Group management**: Create, update, and manage groups
- **Contact management**: Access and manage contacts
- **Status updates**: Handle status messages
- **Media support**: Send and receive images, videos, documents, and audio

## Installation

Prerequisites: Node.js >= 20.0.0

```bash
npm install @riflowsxz/baileys
```

## Quick Start

```javascript
const { makeWASocket, DisconnectReason, useSingleFileAuthState } = require('@riflowsxz/baileys')
const { Boom } = require('@hapi/boom')
const fs = require('fs')

// Use the following auth state to remember the connection
const { state, saveState } = useSingleFileAuthState('./auth_info_multi.json')

async function connectToWhatsApp () {
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true,
    })

    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update
        if(connection === 'close') {
            const shouldReconnect = (lastDisconnect.error)?.output?.statusCode !== DisconnectReason.loggedOut
            console.log('connection closed due to ', lastDisconnect.error, 'reconnecting ', shouldReconnect)
            // reconnect if not logged out
            if(shouldReconnect) {
                connectToWhatsApp()
            }
        } else if(connection === 'open') {
            console.log('opened connection')
        }
    })
    // listen for when the auth credentials is updated
    sock.ev.on('creds.update', saveState)

    // Send a simple text message
    await sock.sendMessage('1234567890@s.whatsapp.net', { text: 'Hello there!' })
}

// run the function
connectToWhatsApp().catch((err) => console.log("error occurred " + err))
```

## Advanced Usage

For more detailed examples and advanced usage, please check the official Baileys documentation at [Baileys GitHub](https://github.com/WhiskeySockets/Baileys).

## API Documentation

TypeScript definitions are included in the package. For comprehensive API documentation, run:

```bash
npm run build:docs
```

## Authentication Methods

This library supports multiple authentication methods:

1. **QR Code Authentication**: Scan QR code with your phone
2. **Pair Protocol**: Pair with your existing WhatsApp account

## Dependencies

This package uses the following key dependencies:

- `@adiwajshing/keyed-db`: For efficient data storage
- `@hapi/boom`: For HTTP-friendly error handling
- `axios`: For HTTP requests
- `protobufjs`: For protocol buffer operations
- `ws`: WebSocket implementation
- And more, as specified in package.json

## Contributing

If you'd like to contribute to this project:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -am 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Create a new Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you encounter any issues or have questions:

1. Check the existing issues in the repository
2. Create a new issue with detailed information about your problem
3. Include your Node.js version and library version when reporting issues

## Acknowledgements

This library is a fork of the original Baileys library by Adhiraj Singh, with modifications by riflowsxz.