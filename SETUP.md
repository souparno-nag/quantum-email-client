# Quick Setup Reference

## ✅ Correct Configuration

### Backend (.env file)
All credentials go here:

```env
# Gmail App Password (NOT regular password!)
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USERNAME=your.email@gmail.com
SMTP_PASSWORD=abcd efgh ijkl mnop  # 16-char App Password

IMAP_SERVER=imap.gmail.com
IMAP_PORT=993
IMAP_USERNAME=your.email@gmail.com
IMAP_PASSWORD=abcd efgh ijkl mnop  # Same App Password

# QKD Simulator (NO API keys needed)
# Use 127.0.0.1 to avoid IPv6 connection issues
QKD_KME_URL=http://127.0.0.1:8000
QKD_SOURCE_KME_ID=KME_001
QKD_TARGET_KME_ID=KME_002
QKD_KEY_SIZE=32
```

### Frontend
- **NO credentials needed**
- Just click "Launch QuMail" button
- Frontend connects to backend automatically

---

## ❌ Common Mistakes

### Mistake 1: Using Regular Gmail Password
**Wrong:** Using your regular Google account password  
**Right:** Use the 16-character App Password from https://myaccount.google.com/apppasswords

### Mistake 2: Entering Credentials in Frontend
**Wrong:** Trying to enter email/password in the frontend UI  
**Right:** All credentials are in the backend's `.env` file

### Mistake 3: Looking for QKD API Keys
**Wrong:** Trying to find or generate API keys for QKD simulator  
**Right:** QKD simulator has no authentication - just runs on port 8000

### Mistake 4: Different Passwords for SMTP and IMAP
**Wrong:** Using different passwords for SMTP and IMAP  
**Right:** Use the SAME Gmail App Password for both

---

## 🔑 Getting Gmail App Password

### Step-by-Step:

1. **Go to Google Account Settings**
   - URL: https://myaccount.google.com/apppasswords
   - Sign in with your regular password

2. **Generate App Password**
   - Select "Mail" from dropdown
   - Select your device/computer name
   - Click "Generate"

3. **Copy the Password**
   - You'll see a 16-character code like: `abcd efgh ijkl mnop`
   - Copy it exactly (spaces don't matter)

4. **Add to .env file**
   ```env
   SMTP_PASSWORD=abcd efgh ijkl mnop
   IMAP_PASSWORD=abcd efgh ijkl mnop
   ```

5. **Save and test**
   - Save the `.env` file
   - Restart the backend
   - Try sending an email

---

## 🚀 Quick Start Checklist

- [ ] Created `.env` file in project root
- [ ] Generated Gmail App Password from Google
- [ ] Added App Password to both SMTP_PASSWORD and IMAP_PASSWORD
- [ ] Set QKD_KME_URL=http://localhost:8000 (no API key needed)
- [ ] Started QKD simulator: `cd qkd-simulator && python main.py`
- [ ] Started backend: `cd backend && python -m uvicorn main:app --port 8001`
- [ ] Started frontend: `cd quantum-mail-frontend && npm start`
- [ ] Clicked "Launch QuMail" button in welcome screen
- [ ] Composed and sent test email
- [ ] Fetched and decrypted email

---

## 📞 Still Having Issues?

### Check Backend Logs
```bash
# If running with test_integration.sh
tail -f logs/backend.log

# If running manually
# Backend will show logs in the terminal
```

### Test Backend Connection
```bash
# Should return {"status":"ok"}
curl http://localhost:8001/health
```

### Test QKD Simulator
```bash
# Should return a list of keys
curl http://localhost:8000/api/v1/keys/enc_keys
```

### Verify .env File
```bash
# Make sure .env exists in project root
cat .env

# Should show your configuration
```

---

## 🎯 Summary

**Where do credentials go?**  
→ Backend's `.env` file

**What password to use?**  
→ Gmail App Password (16 characters)

**Does QKD need API keys?**  
→ No, it's open on localhost:8000

**Does frontend ask for credentials?**  
→ No, it's just a welcome screen

**Where is authentication handled?**  
→ Entirely in the backend using `.env`

---

**For more details, see:**
- [TESTING.md](TESTING.md) - Full testing guide with FAQ
- [README.md](README.md) - Project overview
- [STATUS.md](STATUS.md) - Implementation details
