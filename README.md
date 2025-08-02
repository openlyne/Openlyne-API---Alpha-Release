# Openlyne API - Alpha Release

> A REST-based WhatsApp messaging platform designed for developers across Africa. Send and receive WhatsApp messages programmatically with simple HTTP requests.

[![API Status](https://img.shields.io/badge/API-Alpha-orange)](https://api.openlyne.com)
[![Support](https://img.shields.io/badge/Support-Email-blue)](mailto:alpha-support@openlyne.com)
[![Countries](https://img.shields.io/badge/Countries-3-green)](#supported-countries)

## 🚀 Quick Start

1. [Register for your account](#registration)
2. [Verify your email](#email-verification)
3. [Connect your WhatsApp](#whatsapp-connection)
4. [Make your first API call](#sending-messages)

## 📋 Table of Contents

- [Registration](#registration)
- [Email Verification](#email-verification)
- [API Credentials](#api-credentials)
- [WhatsApp Connection](#whatsapp-connection)
- [Sending Messages](#sending-messages)
- [Receiving Messages](#receiving-messages)
- [API Endpoints](#api-endpoints)
- [Alpha Limitations](#alpha-limitations)
- [Troubleshooting](#troubleshooting)
- [Support](#support)

## 🔐 Registration

1. **Register your account**: [Registration Form](https://register.openlyne.com)
2. **Fill in the form**:
   - Full Name: Your complete name
   - Project Name: Name of your business/project (e.g., "My E-commerce Bot")
   - Email Address: Valid email for verification
3. **Submit** and wait for confirmation

## ✉️ Email Verification

1. Check your email for a message from Openlyne API
2. Copy the 6-digit OTP from the email
3. Visit: [Confirm Email](https://confirm.openlyne.com)
4. Enter your email address and OTP code
5. Click "Verify Email"

## 🔑 API Credentials

After email confirmation, you'll receive your API credentials:

```
API Key: ol_alpha_xxxxxxxxxxxxxxxxxxxxx
Webhook URL: https://webhook.openlyne.com/alpha/your-unique-id
Base API URL: https://api.openlyne.com/v1
```

> ⚠️ **Important**: Keep these credentials secure and never commit them to version control!

## 📱 WhatsApp Connection

1. **Request connection**: [Request Connection](https://connect.openlyne.com)
2. Enter your registered email address
3. Wait for OTP via SMS or WhatsApp (2-3 minutes)
4. **Verify OTP**: [Enter OTP](https://connect.openlyne.com/verify)
5. **Scan QR Code**:
   - Open WhatsApp on your phone
   - Go to Menu (⋮) → "Linked Devices"
   - Tap "Link a Device"
   - Scan the QR code displayed on your screen

## 📤 Sending Messages

### cURL Example

```bash
curl -X POST "https://api.openlyne.com/v1/sendMessage" \
     -H "Authorization: Bearer ol_alpha_xxxxxxxxxxxxxxxxxxxxx" \
     -H "Content-Type: application/json" \
     -d '{
       "phone_number": "+234xxxxxxxxxx", 
       "message": "Hello from Openlyne API! 🚀"
     }'
```

### Python Example

```python
import requests

# Your API credentials
api_key = "ol_alpha_xxxxxxxxxxxxxxxxxxxxx"
base_url = "https://api.openlyne.com/v1"

# Request headers
headers = {
    "Authorization": f"Bearer {api_key}",
    "Content-Type": "application/json"
}

# Message data
data = {
    "phone_number": "+234xxxxxxxxxx",  # Include country code
    "message": "Hello from Openlyne API! 🚀"
}

# Send the message
response = requests.post(f"{base_url}/sendMessage", headers=headers, json=data)

if response.status_code == 200:
    result = response.json()
    print(f"✅ Message sent successfully!")
    print(f"Message ID: {result.get('message_id')}")
else:
    print(f"❌ Error: {response.status_code}")
    print(f"Response: {response.text}")
```

### JavaScript (Node.js) Example

```javascript
const axios = require('axios');

// Your API credentials
const apiKey = "ol_alpha_xxxxxxxxxxxxxxxxxxxxx";
const baseUrl = "https://api.openlyne.com/v1";

// Request headers
const headers = {
    "Authorization": `Bearer ${apiKey}`,
    "Content-Type": "application/json"
};

// Message data
const data = {
    phone_number: "+234xxxxxxxxxx",
    message: "Hello from Openlyne API! 🚀"
};

// Send the message
axios.post(`${baseUrl}/sendMessage`, data, { headers })
    .then(response => {
        console.log("✅ Message sent successfully!");
        console.log(`Message ID: ${response.data.message_id}`);
    })
    .catch(error => {
        console.error("❌ Error:", error.response?.data || error.message);
    });
```

### Expected Response

```json
{
    "success": true,
    "message_id": "msg_alpha_1234567890",
    "status": "sent",
    "phone_number": "+234xxxxxxxxxx",
    "message": "Hello from Openlyne API! 🚀",
    "timestamp": "2024-10-15T14:30:00Z"
}
```

## 📥 Receiving Messages

Set up a webhook endpoint to receive incoming WhatsApp messages.

### Webhook Message Format

```json
{
    "type": "incoming_message",
    "message_id": "msg_incoming_987654321",
    "from": "+234xxxxxxxxxx",
    "from_name": "John Doe",
    "message": "Hi, I need help with my order",
    "timestamp": "2024-10-15T14:35:00Z",
    "chat_id": "234xxxxxxxxxx@c.us"
}
```

### Python Webhook Handler

```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

API_KEY = "ol_alpha_xxxxxxxxxxxxxxxxxxxxx"
BASE_URL = "https://api.openlyne.com/v1"

@app.route('/webhook', methods=['POST'])
def handle_webhook():
    try:
        data = request.json
        
        if data.get('type') == 'incoming_message':
            sender = data['from']
            sender_name = data.get('from_name', 'Unknown')
            message = data['message']
            
            # Send auto-reply
            reply_data = {
                "phone_number": sender,
                "message": f"Hello {sender_name}! Thanks for your message. We'll get back to you soon! 🤖"
            }
            
            headers = {
                "Authorization": f"Bearer {API_KEY}",
                "Content-Type": "application/json"
            }
            
            response = requests.post(f"{BASE_URL}/sendMessage", headers=headers, json=reply_data)
            
            if response.status_code == 200:
                print("✅ Auto-reply sent successfully!")
        
        return jsonify({"status": "success"})
    
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### JavaScript Webhook Handler

```javascript
const express = require('express');
const axios = require('axios');

const app = express();
app.use(express.json());

const API_KEY = "ol_alpha_xxxxxxxxxxxxxxxxxxxxx";
const BASE_URL = "https://api.openlyne.com/v1";

app.post('/webhook', async (req, res) => {
    try {
        const data = req.body;
        
        if (data.type === 'incoming_message') {
            const sender = data.from;
            const senderName = data.from_name || 'Unknown';
            const message = data.message;
            
            // Send auto-reply
            const replyData = {
                phone_number: sender,
                message: `Hello ${senderName}! Thanks for your message. We'll get back to you soon! 🤖`
            };
            
            const headers = {
                "Authorization": `Bearer ${API_KEY}`,
                "Content-Type": "application/json"
            };
            
            await axios.post(`${BASE_URL}/sendMessage`, replyData, { headers });
            console.log("✅ Auto-reply sent successfully!");
        }
        
        res.json({ status: "success" });
    } catch (error) {
        res.status(500).json({ status: "error", message: error.message });
    }
});

app.listen(5000, () => {
    console.log('🚀 Webhook server running on port 5000');
});
```

## 🔌 API Endpoints

### Check Connection Status

```bash
curl -X GET "https://api.openlyne.com/v1/status" \
     -H "Authorization: Bearer ol_alpha_xxxxxxxxxxxxxxxxxxxxx"
```

**Response:**
```json
{
    "connection_status": "connected",
    "phone_number": "+234xxxxxxxxxx",
    "last_seen": "2024-10-15T14:30:00Z",
    "battery_level": 85
}
```

### Get Recent Messages

```bash
curl -X GET "https://api.openlyne.com/v1/messages?limit=10" \
     -H "Authorization: Bearer ol_alpha_xxxxxxxxxxxxxxxxxxxxx"
```

**Response:**
```json
{
    "messages": [
        {
            "message_id": "msg_123",
            "type": "sent",
            "phone_number": "+234xxxxxxxxxx",
            "message": "Hello World",
            "status": "delivered",
            "timestamp": "2024-10-15T14:25:00Z"
        }
    ]
}
```

## ⚠️ Alpha Limitations

### Rate Limits
- **Messages per second**: 1 message/sec
- **Daily limit**: 1,000 messages
- **Monthly limit**: 10,000 messages

### Phone Number Format
Always use international format:
- ✅ **Correct**: `+234xxxxxxxxxx`
- ❌ **Wrong**: `0xxxxxxxxxx` or `234xxxxxxxxxx`

### Supported Countries
- 🇳🇬 Nigeria (+234)
- 🇰🇪 Kenya (+254)
- 🇺🇬 Uganda (+256)

## 🛠️ Troubleshooting

### Common Issues

**🔐 Getting "401 Unauthorized" Error?**
- Verify your API key is correct
- Ensure you're using `Bearer` prefix in Authorization header
- Check if your API key starts with `ol_alpha_`

**📱 WhatsApp Not Connecting?**
- Scan QR code with the same phone that has WhatsApp
- Refresh the page for a new QR code
- Ensure stable internet connection
- Clear browser cache

**📤 Messages Not Sending?**
- Check phone number format (must include country code)
- Verify WhatsApp connection is active (check status endpoint)
- Ensure you haven't exceeded rate limits

**🎣 Webhook Not Receiving Messages?**
- Webhook URL must be publicly accessible (not localhost)
- Endpoint must respond with HTTP 200 status
- Use ngrok for local development testing

### Local Development with ngrok

```bash
# Start your local webhook server
python webhook_handler.py

# In another terminal, expose it publicly
ngrok http 5000

# Use the ngrok URL as your webhook endpoint
# Example: https://abc123.ngrok.io/webhook
```

## 💡 Sample Project Ideas

- **🛒 E-commerce**: Order confirmations and shipping updates
- **💊 Healthcare**: Appointment reminders and medication alerts
- **📚 Education**: Class schedules and assignment notifications
- **🚚 Logistics**: Package tracking and delivery confirmations

## 🔮 Coming Soon

- 📱 Telegram API support
- 🖼️ Media message support (images, documents, audio)
- 📊 Analytics dashboard
- 👥 Multiple WhatsApp account management
- 🎯 Advanced webhook filtering
- 💰 Production pricing tiers

## 📞 Support

### Alpha Testing Support

- **📧 Email**: [info.openlyne@gmail.com](mailto:info.openlyne@gmail.com) (24h response)
- **💬 Telegram**: [t.me/openlyne](https://t.me/openlyne) (Real-time support)
- **📱 WhatsApp**: [wa.me/254101886585](https://wa.me/254101886585) (Direct support)

### Getting Help

When reporting issues, please include:
- Steps to reproduce
- Expected vs actual behavior
- Screenshots or API response examples
- Your API key (last 4 characters only)

**Contact us via:**
- Email: info.openlyne@gmail.com
- Telegram: t.me/openlyne  
- WhatsApp: wa.me/254101886585

---

## 🎉 Ready to Build!

You're now ready to build amazing WhatsApp integrations with Openlyne API. Start with simple message sending, experiment with webhooks, and share your feedback with us.

**Happy coding!** 🚀

---

> **Note**: This is an alpha release. Expect occasional changes and improvements. Your feedback is invaluable for building the best possible API for African developers.

## License

This project is in alpha testing phase. Terms and conditions apply.
