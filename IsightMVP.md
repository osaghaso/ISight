# ISight Real MVP - 7-Day Build Plan
*Help blind people THIS WEEK, not in 3 years*

## Mission
Build the simplest possible vision assistance app that actually helps blind people TODAY.

## Core Principle
**One feature done perfectly > Ten features done poorly**

---

## The Real MVP: "What's This?" Button

### Core Feature
- **Single button**: Take photo, hear description
- **That's it.** But done REALLY well.

---

## Day-by-Day Build Plan

### Day 1-2: The Simplest Possible Thing

Create a single HTML file that works immediately:

```html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>ISight</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            background: #000;
        }
        #captureBtn {
            width: 100%;
            height: 90vh;
            font-size: 72px;
            background: #fff;
            border: none;
            border-radius: 20px;
        }
    </style>
</head>
<body>
    <button id="captureBtn">TAP ANYWHERE TO SEE</button>
    <input type="file" id="camera" accept="image/*" capture="camera" style="display:none">
    
    <script>
        const btn = document.getElementById('captureBtn');
        const camera = document.getElementById('camera');
        
        // Vibrate when pressed
        btn.onclick = () => {
            navigator.vibrate(100);
            camera.click();
        };
        
        camera.onchange = async (e) => {
            btn.textContent = "ANALYZING...";
            
            const base64 = await toBase64(e.target.files[0]);
            
            const response = await fetch('https://api.openai.com/v1/chat/completions', {
                method: 'POST',
                headers: {
                    'Authorization': 'Bearer YOUR-API-KEY',
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    model: "gpt-4o-mini",
                    messages: [{
                        role: "system",
                        content: "You help blind users understand what's in front of them. Be concise but include critical safety info. Start with the most important thing."
                    }, {
                        role: "user",
                        content: [{
                            type: "image_url",
                            image_url: {url: base64}
                        }, {
                            type: "text",
                            text: "What's in this image?"
                        }]
                    }],
                    max_tokens: 150
                })
            });
            
            const data = await response.json();
            speak(data.choices[0].message.content);
            btn.textContent = "TAP ANYWHERE TO SEE";
        };
        
        function speak(text) {
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.rate = 0.9; // Slightly slower
            speechSynthesis.speak(utterance);
        }
        
        function toBase64(file) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.onloadend = () => resolve(reader.result);
                reader.readAsDataURL(file);
            });
        }
    </script>
</body>
</html>
```

**That's your ENTIRE app for Day 1.**

### Day 3: Add Gesture Recognition

Add different prompts for different gestures:

```javascript
// Add these specific prompts for common needs
const prompts = {
    single: "What's in front of me?",
    double: "What text do you see?", // Double tap
    long: "Describe in detail", // Long press
    swipeUp: "Is this safe?" // Swipe up
};

// Detect gesture type
let tapCount = 0;
btn.onclick = () => {
    tapCount++;
    setTimeout(() => {
        if (tapCount === 1) processImage("single");
        if (tapCount === 2) processImage("double");
        tapCount = 0;
    }, 300);
};
```

### Day 4-5: Add Context & Voice Commands

**Location Context:**
```javascript
// Quick location awareness
if ('geolocation' in navigator) {
    navigator.geolocation.getCurrentPosition(pos => {
        // Add to prompt: "User is outdoors" or "User is at home"
        // Changes AI response appropriately
    });
}
```

**Voice Commands:**
```javascript
const recognition = new webkitSpeechRecognition();
recognition.continuous = true;

recognition.onresult = (event) => {
    const command = event.results[event.results.length-1][0].transcript.toLowerCase();
    
    if (command.includes("what") || command.includes("help")) {
        document.getElementById('camera').click();
    }
    if (command.includes("read")) {
        processImage("text");
    }
    if (command.includes("color")) {
        processImage("color");
    }
};

recognition.start();
```

### Day 6: Package as Progressive Web App (PWA)

**manifest.json:**
```json
{
  "name": "ISight - Visual Assistant",
  "short_name": "ISight",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#000000",
  "theme_color": "#ffffff",
  "icons": [{
    "src": "icon-192.png",
    "sizes": "192x192",
    "type": "image/png"
  }]
}
```

**service-worker.js (for offline capability):**
```javascript
self.addEventListener('install', (e) => {
  e.waitUntil(
    caches.open('isight-v1').then((cache) => {
      return cache.addAll([
        '/',
        '/index.html',
        '/manifest.json'
      ]);
    })
  );
});
```

### Day 7: Deploy & Get Users

**Deployment (Free Options):**
1. **Vercel**: vercel.com - Deploy in 2 minutes
2. **Netlify**: netlify.com - Drag and drop deployment
3. **GitHub Pages**: Free with GitHub account

**Where to Find Test Users:**

| Platform | Post Template |
|----------|--------------|
| **r/blind** | "I built a simple vision app, need feedback" |
| **AppleVis Forum** | "New web-based vision assistant for testing" |
| **NFB Mailing Lists** | "Seeking testers for accessibility app" |
| **Local Facebook Groups** | "Free vision app for blind community" |

**Message Template:**
```
Hi, I'm building a free app to help identify objects/text. 
It's very basic - just tap and hear what's in front of you. 
Looking for 5 people to test it this week and tell me what 
features to add next. No sign-up required.

Try it here: [your-url]

What works:
- Take photo and hear description
- Text reading (double tap)
- Voice commands ("what do you see?")

What I need from you:
- Does it work on your phone?
- What feature do you need most?
- Any bugs or issues?

Thank you!
```

---

## Week 2: Feature Prioritization

Based on user feedback, implement in this order:

### Priority 1 - Safety Features
- [ ] Medicine label reading
- [ ] Expiration date detection
- [ ] Money identification
- [ ] Light detection (on/off)

### Priority 2 - Daily Living
- [ ] Color identification for clothes
- [ ] Finding dropped items
- [ ] Reading mail/packages
- [ ] Appliance settings

### Priority 3 - Social Features
- [ ] People counting in room
- [ ] Facial expression detection
- [ ] Scene context ("busy restaurant")

---

## Success Metrics

### Week 1
- ✅ 10 test users
- ✅ 5 feedback responses
- ✅ 1 feature request implemented

### Week 2
- ✅ 50 active users
- ✅ 3 new features based on feedback
- ✅ <2 second response time

### Week 3
- ✅ 100 active users
- ✅ Daily active usage from 10+ users
- ✅ First accessibility organization contact

### Week 4
- ✅ 200+ users
- ✅ First donation or grant opportunity
- ✅ Partnership discussion started

---

## Budget (First Month)

| Item | Cost | Note |
|------|------|------|
| Domain | $12 | Namecheap |
| OpenAI API | $20 | ~1000 requests |
| Hosting | $0 | Vercel free tier |
| **Total** | **$32** | |

---

## Technical Optimizations

### API Cost Reduction
```javascript
// Use gpt-4o-mini for most requests
const model = isDetailedRequest ? "gpt-4o" : "gpt-4o-mini";

// Compress images before sending
function compressImage(base64, maxWidth = 800) {
    // Resize logic here
    return compressedBase64;
}
```

### Performance Improvements
```javascript
// Cache recent descriptions
const cache = new Map();

function getCacheKey(imageData) {
    // Simple hash of first 1000 chars
    return imageData.substring(0, 1000);
}

// Preload TTS voices
window.speechSynthesis.getVoices();
```

---

## Common Problems & Solutions

### Problem: "API key is exposed in frontend"
**Solution for MVP**: It's fine for testing. Add rate limiting in Week 2:
```javascript
// Simple rate limiting
const userRequests = new Map();
function rateLimitCheck(userId) {
    const count = userRequests.get(userId) || 0;
    if (count > 20) return false; // 20 per day limit
    userRequests.set(userId, count + 1);
    return true;
}
```

### Problem: "Voice speaks over itself"
**Solution**:
```javascript
function speak(text) {
    speechSynthesis.cancel(); // Stop current speech
    const utterance = new SpeechSynthesisUtterance(text);
    speechSynthesis.speak(utterance);
}
```

### Problem: "Works on iPhone but not Android"
**Solution**: Use standard APIs:
```javascript
// Use standard camera API
<input type="file" accept="image/*" capture="environment">

// Fallback for speech
if (!('speechSynthesis' in window)) {
    // Use alternative TTS service
}
```

---

## Future Roadmap (After MVP Success)

### Month 2: Enhanced Features
- Continuous mode (photo every 3 seconds)
- Offline fallback with TensorFlow.js
- Multi-language support
- User accounts and preferences

### Month 3: Platform Expansion
- Native iOS app (TestFlight)
- Native Android app (APK direct distribution)
- Desktop browser extension
- API for developers

### Month 6: Hardware Integration
- Smart glasses SDK
- Partnership with existing manufacturers
- Open-source hardware reference design

---

## Key Principles to Remember

1. **Ship something TODAY** - Perfect is the enemy of good
2. **Users > Features** - 10 happy users > 1000 features
3. **Feedback > Assumptions** - Let users tell you what to build
4. **Simple > Complex** - One button that works > Ten that confuse
5. **Free > Paid** (initially) - Remove all barriers to trying it

---

## Your Immediate Action Items

### Right Now (Next 10 Minutes)
1. [ ] Copy the HTML code from Day 1
2. [ ] Save as `isight.html`
3. [ ] Get OpenAI API key
4. [ ] Replace `YOUR-API-KEY` in code
5. [ ] Open on your phone
6. [ ] Take one photo and verify it works

### Today
1. [ ] Test blindfolded for 10 minutes
2. [ ] Fix the first annoying thing
3. [ ] Deploy to Vercel/Netlify
4. [ ] Share with ONE person

### This Week
1. [ ] Get 10 people to try it
2. [ ] Implement top requested feature
3. [ ] Post in r/blind for feedback
4. [ ] Set up analytics to track usage

---

## Contact & Support

**Need help?** Common issues:
- API key not working: Check OpenAI dashboard for credits
- Speech not working: Test in Chrome/Safari (not Firefox)
- Camera not opening: Needs HTTPS (use deployment URL, not local file)

**Remember**: This simple HTML file will help more blind people this week than a year of planning smart glasses.

OpenAI-API-KEY -"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"

---

*Stop planning. Start building. Ship TODAY.*