# PC Maintenance Guide for Developers

> **Purpose:** Keep my dev machine running smoothly without performance issues
> 
> **Last Updated:** October 31, 2025
> 
> **My Setup:** Windows 11, WSL2/Ubuntu, Docker Desktop, Node.js development

---

## 📅 Monthly Checklist (Set calendar reminder for 1st of month!)

### Quick Health Check (5 minutes)
- [ ] **Check disk space**: Windows Settings → System → Storage
  - ✅ Goal: Stay under 75% full (< 715 GB / 954 GB)
  - ⚠️ Warning: Over 80% = performance issues
  - 🚨 Critical: Over 90% = system slowdown
  
- [ ] **Run Disk Cleanup**:
  ```
  1. Press Windows key
  2. Type "Disk Cleanup"
  3. Select C: drive
  4. Click "Clean up system files"
  5. Check: Temporary files, Delivery Optimization, Thumbnails, Previous Windows installations
  6. Click OK
  ```

- [ ] **Restart if needed**: Check Task Manager → Performance → Uptime
  - If uptime > 7 days, restart

- [ ] **Empty Recycle Bin**

---

## 🗓️ Quarterly Checklist (Every 3 months)

### Storage Deep Dive
- [ ] **Clean Downloads folder**: `C:\Users\mikeb\Downloads`
  - Delete or move files to external drive
  
- [ ] **Review project folders**:
  - Look for old `node_modules` folders (can be 500MB+ each)
  - Delete from old/abandoned projects
  - Can always `npm install` again if needed

- [ ] **Uninstall unused programs**:
  - Settings → Apps → Installed apps
  - Sort by size
  - Remove anything you haven't used in 6+ months

### Developer-Specific Cleanup

- [ ] **Docker cleanup** (if using Docker):
  ```bash
  docker system prune -a --volumes
  ```
  - Removes unused containers, images, and volumes

- [ ] **WSL/Ubuntu cleanup**:
  ```bash
  # Open Ubuntu terminal
  sudo apt autoremove    # Remove unused packages
  sudo apt clean         # Clear package cache
  
  # Check what's using space
  du -h --max-depth=1 ~ | sort -hr | head -20
  ```

- [ ] **Git cleanup** (optional):
  ```bash
  # In each project directory
  git gc --aggressive --prune=now
  ```

- [ ] **Clear browser caches** (if massive):
  - Chrome: Settings → Privacy → Clear browsing data
  - Edge: Settings → Privacy → Choose what to clear

---

## 🚨 When PC Feels Slow - Troubleshooting

### Step 1: Check Task Manager
```
Press: Ctrl + Shift + Esc
```

**Look for:**
- **CPU tab**: What process is using >50% CPU?
- **Memory tab**: Is RAM >90% used?
- **Disk tab**: Is disk at 100% usage?
- **Startup tab**: Disable unnecessary startup programs

### Step 2: Check Disk Space
```
Settings → System → Storage
```
- If >80% full, run Disk Cleanup
- If >90% full, find and delete large files immediately

### Step 3: Check for Updates
```
Settings → Windows Update
```
- Install pending updates
- Restart if required

### Step 4: Check VPN
- Temporarily disable VPN
- Test if performance improves
- Some VPNs can slow things down

### Step 5: Restart
- Simple but effective
- Clears memory leaks
- Resets system processes

---

## 🔍 Finding Large Files

### Method 1: Windows Storage Settings
```
Settings → System → Storage → Click on C: drive → Show more categories
```
- Shows breakdown by category
- Can drill down into specific folders

### Method 2: File Explorer Search
```
1. Open File Explorer (Windows + E)
2. Navigate to C:\Users\mikeb\
3. In search box, click "Size" dropdown
4. Select "Gigantic (>128 MB)" or "Huge (16-128 MB)"
5. Sort by size
```

### Method 3: Command Line (for nerds)
```powershell
# Find largest folders in user directory
Get-ChildItem -Path C:\Users\mikeb -Directory -Recurse -ErrorAction SilentlyContinue | 
  ForEach-Object { 
    $size = (Get-ChildItem $_.FullName -Recurse -ErrorAction SilentlyContinue | 
             Measure-Object -Property Length -Sum).Sum
    [PSCustomObject]@{
      Path = $_.FullName
      SizeGB = [math]::Round($size / 1GB, 2)
    }
  } | Sort-Object SizeGB -Descending | Select-Object -First 20
```

---

## 💾 Space Management Guidelines

### Current System Specs
- **Total:** 954 GB
- **Sweet spot:** < 715 GB used (75%)
- **Warning zone:** 760-860 GB (80-90%)
- **Danger zone:** > 860 GB (90%+)

### What Takes Up Space (My Common Culprits)

1. **WSL/Ubuntu disk** (~135 GB)
   - Docker images/containers
   - Node projects with node_modules
   - Build artifacts

2. **Development projects** (varies)
   - Each node_modules: 200-800 MB
   - Docker images: 500 MB - 2 GB each
   - Build outputs

3. **Crypto mining data** (learned this the hard way! 😅)
   - Old mining plots can be 100+ GB
   - Always check before installing mining software

4. **Downloads folder** (varies)
   - ISOs, installers, zip files pile up

### Safe to Delete
- ✅ Temporary files
- ✅ Delivery Optimization files
- ✅ Old Windows installations
- ✅ Downloads folder (after reviewing)
- ✅ node_modules from old projects
- ✅ Docker images/containers not in use
- ✅ Old crypto mining data

### DON'T Delete (unless you know what you're doing)
- ❌ System files
- ❌ Program Files folders
- ❌ AppData (unless specific cleanup)
- ❌ Active project files

---

## 🛠️ Useful Commands Reference

### Windows PowerShell
```powershell
# Check disk space
Get-PSDrive C

# Force empty Recycle Bin
Clear-RecycleBin -Force

# Optimize WSL disk (after shutting down WSL)
wsl --shutdown
Optimize-VHD -Path "$env:LOCALAPPDATA\Packages\CanonicalGroupLimited.Ubuntu20.04onWindows_*\LocalState\ext4.vhdx" -Mode Full
```

### Ubuntu/WSL
```bash
# Check disk usage
df -h

# Find large directories
du -h --max-depth=1 ~ | sort -hr | head -20

# Clean package manager
sudo apt autoremove
sudo apt clean

# Find and remove old node_modules
find ~ -name "node_modules" -type d -prune

# Remove specific node_modules
rm -rf ./node_modules
```

### Docker
```bash
# See all images
docker images

# See all containers
docker ps -a

# Nuclear option - remove everything
docker system prune -a --volumes

# More conservative - remove stopped containers and unused images
docker system prune
```

---

## 📝 Notes & Lessons Learned

### October 31, 2025
- **Problem:** PC super slow, everything lagging
- **Cause:** Disk at 93% full (886/954 GB)
- **Solution:** Found 289 GB of old crypto mining data + cleaned up projects
- **Lesson:** Check disk space monthly! Performance tanks above 80%

### Space hogs I found:
- `C:\Users\mikeb\post` - 183 GB of crypto mining data
- `C:\Users\mikeb\cat-token-box` - 106 GB (node_modules + docker stuff)
- Ubuntu WSL - 135 GB (projects + docker)

---

## 🎯 Pro Tips

1. **Set a monthly reminder** - Add to calendar: "PC Maintenance Day"
2. **Before starting new projects** - Check disk space first
3. **After finishing projects** - Delete node_modules if not actively using
4. **Keep external drive handy** - For archiving old projects
5. **Learn to use `ncdu`** - Better disk usage analyzer for Linux
   ```bash
   sudo apt install ncdu
   ncdu ~
   ```

---

## 🔗 Useful Links

- [Docker Docs - System Prune](https://docs.docker.com/engine/reference/commandline/system_prune/)
- [WSL Disk Management](https://learn.microsoft.com/en-us/windows/wsl/disk-space)

---

## ✅ Quick Reference Card

**PC feels slow?**
1. Ctrl+Shift+Esc → Check Task Manager
2. Settings → Storage → Check disk space
3. Restart PC
4. Still slow? Check this guide!

**Monthly 5-minute routine:**
1. Check disk space (< 75%?)
2. Run Disk Cleanup
3. Restart if uptime > 7 days
4. Empty Recycle Bin

**Common fixes:**
- Slow = Check disk space first!
- High CPU = Check Task Manager → End heavy processes
- Can't install = Probably out of disk space
- WSL slow = Clean up Docker + node_modules

---

*Last major cleanup: October 31, 2025*
*Next scheduled: December 1, 2025*
