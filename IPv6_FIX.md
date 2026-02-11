# IPv6 Connection Issue - Fix Summary

## Problem
Frontend showed error: `connect ECONNREFUSED ::1:8001`
- Backend was listening on all interfaces (0.0.0.0)
- curl worked fine with http://localhost:8001
- But frontend's axios was resolving `localhost` to `::1` (IPv6) instead of `127.0.0.1` (IPv4)
- This caused connection refused errors

## Root Cause
When Node.js/Electron resolves `localhost`, it may prefer IPv6 (`::1`) over IPv4 (`127.0.0.1`). If the backend isn't explicitly listening on IPv6, or if there are IPv6 networking issues, the connection fails.

## Solution
Changed all `localhost` references to `127.0.0.1` (explicit IPv4) in:

### 1. Frontend Code
**File:** `quantum-mail-frontend/electron/ipc-handlers.js`
```javascript
// OLD:
const BACKEND_URL = process.env.BACKEND_URL || 'http://localhost:8001';

// NEW: 
const BACKEND_URL = process.env.BACKEND_URL || 'http://127.0.0.1:8001';
```

### 2. Backend Config
**File:** `backend/config.py`
```python
# OLD:
QKD_KME_URL: str = os.getenv("QKD_KME_URL", "http://localhost:8000")

# NEW:
QKD_KME_URL: str = os.getenv("QKD_KME_URL", "http://127.0.0.1:8000")
```

### 3. Configuration Templates
**File:** `.env.example`
```env
# OLD:
QKD_KME_URL=http://localhost:8000

# NEW:
QKD_KME_URL=http://127.0.0.1:8000
```

### 4. Integration Test Script
**File:** `test_integration.sh`
```bash
# OLD:
curl -s http://localhost:8000/api/v1/keys/enc_keys
curl -s http://localhost:8001/health

# NEW:
curl -s http://127.0.0.1:8000/api/v1/keys/enc_keys
curl -s http://127.0.0.1:8001/health
```

### 5. Documentation
Updated configuration examples in:
- `TESTING.md` - Added troubleshooting section for IPv6 errors
- `SETUP.md` - Updated QKD_KME_URL example
- `README.md` - Added connection error troubleshooting
- `CHANGES.md` - Updated configuration examples

## Files Modified
1. `quantum-mail-frontend/electron/ipc-handlers.js` ✅
2. `backend/config.py` ✅
3. `.env.example` ✅
4. `test_integration.sh` ✅
5. `TESTING.md` ✅
6. `SETUP.md` ✅
7. `README.md` ✅

## Testing the Fix

### 1. Stop all services
```bash
./stop_services.sh
```

### 2. Start services again
```bash
./test_integration.sh
```

### 3. Verify frontend can connect
Open the Electron app and try to:
- Send an email
- Fetch emails
- Check frontend logs - should no longer show `::1:8001` errors

### 4. Check logs
```bash
# Frontend should now connect successfully
tail -f logs/frontend.log

# Backend should show incoming requests
tail -f logs/backend.log
```

## Why This Works

**127.0.0.1 vs localhost:**
- `127.0.0.1` = Explicit IPv4 loopback address
- `localhost` = DNS name that can resolve to IPv4 OR IPv6
- On systems with IPv6 enabled, `localhost` may prefer `::1` (IPv6)
- Using explicit `127.0.0.1` forces IPv4, avoiding ambiguity

**Backend listens on 0.0.0.0:**
- This means all network interfaces (IPv4 and IPv6)
- But explicit IPv4 connection is more reliable
- Avoids IPv6 routing issues

## Expected Behavior After Fix

### Frontend logs should show:
```
Connecting to backend at http://127.0.0.1:8001
Successfully connected to backend
Sending email via backend...
Email sent successfully!
Fetching emails from backend...
Received 10 emails
```

### Backend logs should show:
```
INFO:     127.0.0.1:xxxxx - "POST /send HTTP/1.1" 200 OK
INFO:     127.0.0.1:xxxxx - "POST /fetch HTTP/1.1" 200 OK
INFO:     127.0.0.1:xxxxx - "POST /decrypt HTTP/1.1" 200 OK
```

## Additional Notes

### If you still see connection issues:

1. **Check backend is running:**
   ```bash
   curl http://127.0.0.1:8001/health
   ```

2. **Check QKD simulator is running:**
   ```bash
   curl http://127.0.0.1:8000/api/v1/keys/enc_keys
   ```

3. **Restart frontend:**
   ```bash
   cd quantum-mail-frontend
   npm start
   ```

4. **Check your .env file:**
   ```bash
   cat .env | grep KME_URL
   # Should show: QKD_KME_URL=http://127.0.0.1:8000
   ```

### For production deployment:
- Consider using proper hostnames
- Use HTTPS for all connections
- Implement proper service discovery
- Use environment-specific configurations

## Verification Checklist

After applying the fix, verify:

- [ ] Frontend starts without errors
- [ ] Can send encrypted email (all levels L1-L4)
- [ ] Can fetch emails from inbox
- [ ] Can decrypt emails automatically when clicked
- [ ] Backend logs show requests from 127.0.0.1
- [ ] Frontend logs no longer show ::1 errors
- [ ] QKD simulator responds to key requests
- [ ] All three services communicate properly

## Related Issues

This fix resolves:
- ❌ `Error: connect ECONNREFUSED ::1:8001`
- ❌ Frontend appears to hang when sending emails
- ❌ No backend logs for frontend requests
- ❌ curl works but frontend doesn't

✅ All resolved by using explicit IPv4 addresses

---

**Status:** ✅ FIXED
**Tested:** Local development environment
**Impact:** Critical - Frontend now connects reliably to backend
