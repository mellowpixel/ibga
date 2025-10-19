# IBGA Fork - Deployment Guide

This is a forked version of the IBGA (Interactive Brokers Gateway Automation) with enhanced automation scripts that properly handle:

- ✅ Auto restart warning dialogs
- ✅ Two-factor authentication (TOTP)
- ✅ Mobile Authenticator app selection
- ✅ Live Trading mode support

## 🚀 Deployment Options

### Option 1: GitHub Container Registry (Recommended)

1. **Push your fork to GitHub**:
   ```bash
   git remote add origin https://github.com/yourusername/ibga.git
   git push -u origin main
   ```

2. **Enable GitHub Actions**: The workflow will automatically build and push to `ghcr.io/yourusername/ibga:latest`

3. **Update your algo-trader docker-compose.yml**:
   ```yaml
   ibkr-gateway:
     image: ghcr.io/yourusername/ibga:latest
   ```

### Option 2: Docker Hub

1. **Build and tag locally**:
   ```bash
   docker build -t yourusername/ibga:latest .
   ```

2. **Push to Docker Hub**:
   ```bash
   docker push yourusername/ibga:latest
   ```

3. **Update your algo-trader docker-compose.yml**:
   ```yaml
   ibkr-gateway:
     image: yourusername/ibga:latest
   ```

### Option 3: Local Build

1. **Build locally in your algo-trader project**:
   ```bash
   cd algo-trader/aws
   docker build -t ibga:latest ../../ibga/
   ```

2. **Update docker-compose.yml**:
   ```yaml
   ibkr-gateway:
     image: ibga:latest
   ```

## 🔧 Configuration

### Environment Variables

Make sure to set these in your `.env` file or environment:

```bash
IB_USERNAME=your_username
IB_PASSWORD=your_password
IB_TOTP_KEY=your_totp_secret_key  # For Live Trading
IB_REGION=Europe
IB_TIMEZONE=Europe/Dublin
IB_LOGINTAB=IB API
IB_LOGINTYPE=Live Trading  # or Paper Trading
IB_LOGOFF=11:55 PM
IB_APILOG=detail
IB_LOGLEVEL=Error
```

### Live Trading vs Paper Trading

- **Live Trading**: Requires `IB_TOTP_KEY` for two-factor authentication
- **Paper Trading**: Uses auto restart warning dialog handling

## 🧪 Testing

Use the included test script to verify the connection:

```bash
cd ibga
python test_connection.py
```

## 📝 Changes Made

### Enhanced Automation Scripts

1. **Fixed line endings**: Added `dos2unix` to handle Windows/Linux line ending differences
2. **Improved dialog detection**: Enhanced component detection for various UI elements
3. **Two-factor authentication**: Added support for TOTP-based Mobile Authenticator app
4. **Auto restart warning**: Added handler for auto restart confirmation dialogs
5. **Better error handling**: Improved logging and error recovery

### Key Script Improvements

- `_run_ibg.sh`: Enhanced with proper TOTP handling and dialog management
- `dockerfile`: Added `dos2unix` package for line ending compatibility
- `docker-compose.yaml`: Updated with build configuration

## 🔄 Updates

To update your forked image:

1. **GitHub Container Registry**: Push changes to your repository
2. **Docker Hub**: Rebuild and push with new tag
3. **Local**: Rebuild the image in your project

## 🆘 Troubleshooting

### Connection Issues
- Verify port 4000 is accessible
- Check that IBKR Gateway API is enabled
- Ensure no other client is using the same clientId

### Authentication Issues
- Verify `IB_TOTP_KEY` is correct for Live Trading
- Check that Mobile Authenticator app is configured in IBKR account
- For Paper Trading, ensure auto restart is properly configured

### Dialog Issues
- Check logs for dialog detection messages
- Verify environment variables are properly set
- Ensure fresh container start (delete `run/program` and `run/settings` folders)
