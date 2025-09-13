# Contact Form Email Setup Guide

This guide provides multiple solutions to send contact form submissions to support@mistsage.com.

## 📧 Available Solutions

### 1. **Formspree** (Recommended - Easy Setup)
### 2. **Netlify Forms** (If using Netlify hosting)
### 3. **EmailJS** (Client-side solution)
### 4. **Web3Forms** (Simple and free)
### 5. **Custom Backend** (Advanced)

---

## 🚀 Option 1: Formspree (Recommended)

**Cost**: Free for 50 submissions/month, $10/month for unlimited
**Setup Time**: 5 minutes
**Best for**: Static websites with reliable email delivery

### Setup Steps:

1. **Sign up at formspree.io**
2. **Create new form** with endpoint: `support@mistsage.com`
3. **Get your form endpoint** (e.g., `https://formspree.io/f/xpznkqyw`)

### Update JavaScript:

```javascript
// Replace the contact form handling section in script.js
const contactForm = document.getElementById('contactForm');
if (contactForm) {
    contactForm.addEventListener('submit', function(e) {
        e.preventDefault();
        
        // Get form data
        const formData = new FormData(contactForm);
        const data = Object.fromEntries(formData);
        
        // Basic validation
        if (!data.name || !data.email || !data.service || !data.message) {
            showNotification('Please fill in all required fields.', 'error');
            return;
        }
        
        // Email validation
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(data.email)) {
            showNotification('Please enter a valid email address.', 'error');
            return;
        }
        
        // Show loading state
        const submitBtn = contactForm.querySelector('button[type="submit"]');
        const originalText = submitBtn.textContent;
        submitBtn.textContent = 'Sending...';
        submitBtn.disabled = true;
        
        // Submit to Formspree
        fetch('https://formspree.io/f/YOUR_FORM_ID', {
            method: 'POST',
            body: formData,
            headers: {
                'Accept': 'application/json'
            }
        }).then(response => {
            if (response.ok) {
                showNotification('Thank you for your message! We will get back to you soon.', 'success');
                contactForm.reset();
            } else {
                throw new Error('Network response was not ok');
            }
        }).catch(error => {
            showNotification('Sorry, there was an error sending your message. Please try again.', 'error');
            console.error('Error:', error);
        }).finally(() => {
            // Reset button
            submitBtn.textContent = originalText;
            submitBtn.disabled = false;
        });
    });
}
```

### Update HTML Form:

```html
<!-- Add action and method to your form -->
<form id="contactForm" action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
    <!-- Keep existing form fields -->
    <!-- Add honeypot for spam protection -->
    <input type="text" name="_gotcha" style="display:none" />
</form>
```

---

## 🌐 Option 2: Netlify Forms (If using Netlify)

**Cost**: Free for 100 submissions/month
**Setup Time**: 2 minutes
**Best for**: Sites hosted on Netlify

### Update HTML:

```html
<form id="contactForm" name="contact" method="POST" data-netlify="true" data-netlify-honeypot="bot-field">
    <input type="hidden" name="form-name" value="contact" />
    <!-- Honeypot -->
    <p style="display: none;">
        <label>Don't fill this out: <input name="bot-field" /></label>
    </p>
    
    <!-- Keep existing form fields -->
    <!-- Add hidden field for email notification -->
    <input type="hidden" name="_to" value="support@mistsage.com" />
</form>
```

### JavaScript remains the same, but add:

```javascript
// Netlify form submission
fetch('/', {
    method: 'POST',
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams(formData).toString()
})
```

---

## 📨 Option 3: EmailJS (Client-side)

**Cost**: Free for 200 emails/month
**Setup Time**: 10 minutes
**Best for**: Complete client-side solution

### Setup Steps:

1. **Sign up at emailjs.com**
2. **Create email service** (Gmail, Outlook, etc.)
3. **Create email template**
4. **Get your Service ID, Template ID, and Public Key**

### Add EmailJS SDK:

```html
<!-- Add to head section of index.html -->
<script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/email.min.js"></script>
```

### Update JavaScript:

```javascript
// Initialize EmailJS
(function(){
    emailjs.init("YOUR_PUBLIC_KEY");
})();

// Contact form handling
const contactForm = document.getElementById('contactForm');
if (contactForm) {
    contactForm.addEventListener('submit', function(e) {
        e.preventDefault();
        
        // Validation code (same as above)...
        
        // Show loading
        const submitBtn = contactForm.querySelector('button[type="submit"]');
        const originalText = submitBtn.textContent;
        submitBtn.textContent = 'Sending...';
        submitBtn.disabled = true;
        
        // Send email
        emailjs.sendForm('YOUR_SERVICE_ID', 'YOUR_TEMPLATE_ID', contactForm)
            .then(() => {
                showNotification('Thank you for your message! We will get back to you soon.', 'success');
                contactForm.reset();
            }, (error) => {
                showNotification('Sorry, there was an error sending your message. Please try again.', 'error');
                console.error('EmailJS error:', error);
            })
            .finally(() => {
                submitBtn.textContent = originalText;
                submitBtn.disabled = false;
            });
    });
}
```

### Email Template (in EmailJS):

```
Subject: New Contact Form Submission - MistSage

Hello,

You have received a new message from your MistSage website:

Name: {{name}}
Email: {{email}}
Company: {{company}}
Service: {{service}}
Message: {{message}}

---
This email was sent from the MistSage contact form.
```

---

## 🔄 Option 4: Web3Forms (Simple & Free)

**Cost**: Free forever
**Setup Time**: 3 minutes
**Best for**: Quick setup with reliable delivery

### Setup Steps:

1. **Get API key** from web3forms.com
2. **Update your form**

### Update HTML:

```html
<form id="contactForm" action="https://api.web3forms.com/submit" method="POST">
    <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY">
    <input type="hidden" name="subject" value="New Contact Form Submission - MistSage">
    <input type="hidden" name="from_name" value="MistSage Website">
    
    <!-- Keep existing form fields -->
    
    <!-- Honeypot -->
    <input type="checkbox" name="botcheck" class="hidden" style="display: none;">
</form>
```

### Update JavaScript:

```javascript
// Web3Forms submission
fetch('https://api.web3forms.com/submit', {
    method: 'POST',
    body: formData
}).then(response => response.json())
.then(data => {
    if (data.success) {
        showNotification('Thank you for your message! We will get back to you soon.', 'success');
        contactForm.reset();
    } else {
        throw new Error('Form submission failed');
    }
})
```

---

## 🛠️ Option 5: Custom Backend (Advanced)

**Cost**: Varies by hosting
**Setup Time**: 30+ minutes
**Best for**: Full control and customization

### Node.js + Express Example:

```javascript
// server.js
const express = require('express');
const nodemailer = require('nodemailer');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

// Email configuration
const transporter = nodemailer.createTransporter({
    service: 'gmail', // or your email provider
    auth: {
        user: 'your-email@gmail.com',
        pass: 'your-app-password'
    }
});

app.post('/submit-form', async (req, res) => {
    const { name, email, company, service, message } = req.body;
    
    const mailOptions = {
        from: 'your-email@gmail.com',
        to: 'support@mistsage.com',
        subject: 'New Contact Form Submission - MistSage',
        html: `
            <h2>New Contact Form Submission</h2>
            <p><strong>Name:</strong> ${name}</p>
            <p><strong>Email:</strong> ${email}</p>
            <p><strong>Company:</strong> ${company}</p>
            <p><strong>Service:</strong> ${service}</p>
            <p><strong>Message:</strong> ${message}</p>
        `
    };
    
    try {
        await transporter.sendMail(mailOptions);
        res.json({ success: true });
    } catch (error) {
        res.status(500).json({ success: false, error: error.message });
    }
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## 💡 Recommended Implementation

### For Quick Setup (5 minutes):
**Use Formspree** - Most reliable with great free tier

### For Netlify Users:
**Use Netlify Forms** - Built-in and free

### For Full Control:
**Use EmailJS** - Works with any hosting, good free tier

---

## 🔧 Implementation Steps

1. **Choose your preferred option**
2. **Update the JavaScript code** (replace current form handler)
3. **Update HTML if needed** (add action/method attributes)
4. **Test the form** thoroughly
5. **Monitor submissions** in your chosen service dashboard

---

## 📋 Testing Checklist

- [ ] Form validation works
- [ ] Email arrives at support@mistsage.com
- [ ] Success/error messages display correctly
- [ ] Form resets after successful submission
- [ ] Mobile compatibility
- [ ] Spam protection enabled

---

## 🚨 Important Notes

- **Test thoroughly** before going live
- **Set up email filtering** to avoid spam
- **Monitor your quota** on free tiers
- **Keep backup** of original code
- **Add CAPTCHA** if you get spam

---

Would you like me to implement any specific option for you?
