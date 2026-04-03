# DAT Files Setup Guide

## Where to Place DAT Files

AC2D looks for DAT files in the **same directory as the executable**.

### Executable Location
Based on your build output, the executable is at:
```
C:\Repo\AC2D\bin\AC2DTest-Debug.exe
```

### DAT Files Location
Place your DAT files in:
```
C:\Repo\AC2D\bin\
```

## Required DAT Files

### Required:
- **`client_portal.dat`** - Contains models, textures, animations, palettes, spell tables, skill tables, etc.
  - This is the main DAT file and is required for the client to function

### Optional:
- **`client_highres.dat`** - High-resolution textures (if available)
  - The client will work without this, but will use it if present

## How AC2D Finds DAT Files

The client uses `GetModuleFileName()` to find the executable's directory, then looks for DAT files there:

```cpp
// From cTurbineFile.cpp
GetModuleFileName(NULL, szEXEPathname, _MAX_PATH);
*(strrchr(szEXEPathname, '\\')+1) = 0;  // Strip filename, keep directory
m_szACPath = szEXEPathname;  // This is where DAT files are loaded from
```

## File Structure

Your `bin` directory should look like:
```
C:\Repo\AC2D\bin\
├── AC2DTest-Debug.exe
├── client_portal.dat          ← Required
└── client_highres.dat          ← Optional
```

## Getting DAT Files

DAT files come from the official Asheron's Call client installation:
- Look in your Asheron's Call installation directory
- Copy `client_portal.dat` and optionally `client_highres.dat`
- Place them in the `bin` directory with the executable

## Testing Without DAT Files

With the fixes we just made, the client will now:
- ✅ Start even if some DAT entries are missing
- ✅ Connect to the server (networking doesn't require DAT files)
- ⚠️ Some features may not work (spell info, skill info, etc.) but connection will work

## Verification

After placing DAT files, you should:
1. Run the client
2. Check that it doesn't crash on startup
3. The client should now be able to load spell tables, skill tables, etc.

## Notes

- DAT files are large (hundreds of MB to several GB)
- They contain static game assets (models, textures, etc.)
- The server doesn't send these - they're loaded locally to reduce server load
- This is the correct architecture for game clients

