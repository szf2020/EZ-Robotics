# Robot Control UI

Web-based control interface for FluidNC/GRBL CNC machines with 3D visualization, G-code editing, and real-time monitoring.

## Features

### Core Functionality
- **Serial Communication**: Direct connection to FluidNC/GRBL controllers via WebSerial API
- **G-code Editor**: Monaco-based editor with syntax highlighting, validation, and line-by-line execution tracking
- **3D Workspace Viewer**: Real-time toolpath visualization with multiple rendering modes
- **Machine Control**: Jog controls, homing, work coordinate setting, and feed rate override
- **Status Monitoring**: Real-time position, speed, and machine state tracking
- **Console**: Direct command interface with GRBL/FluidNC command support

### Interface
- **Dockable Panels**: Fully customizable workspace with drag-and-drop panels
- **Multiple Layouts**: Predefined layouts for different workflows (coding, visualization, monitoring)
- **Theme Support**: Multiple color themes including dark, light, dracula, abyss
- **Responsive Design**: Adapts to different screen sizes with minimum panel widths

### Advanced Features
- **Plugin System**: Load custom plugins from ZIP files with panel integration
- **Python Backend Support**: Optional Python backend for advanced processing (port 8001)
- **STL File Support**: Load and position STL models in the workspace
- **Transform Tools**: Scale, rotate, and translate G-code coordinates
- **Acceleration Profiles**: Configure and visualize motion dynamics

## Requirements

- **Browser**: Chrome or Edge (requires WebSerial API support)
- **Node.js**: v14 or higher for development
- **Machine**: FluidNC or GRBL-compatible CNC controller

## Installation

```bash
# Clone the repository
git clone [repository-url]
cd robot-control-ui

# Install dependencies
npm install

# Start development server
npm start
```

The application will open at `http://localhost:3000`

## Usage

### Connecting to Your Machine

1. Click **Connect** in the toolbar
2. Select your serial port when prompted
3. Choose baud rate (default: 115200)
4. The status indicator will turn green when connected

### Basic Operation

**Jogging**
- Use the directional controls in the Control Panel
- Click for single step, hold for continuous movement
- Adjust step size and feed rate as needed

**Running G-code**
1. Load G-code via the editor or file upload
2. Click **Send to Robot** in the editor toolbar
3. Use pause/resume/stop controls during execution

**Work Coordinates**
- Click **Set Zero** to set current position as work origin
- Use **Go To Zero** to return to work origin
- Switch between Work and Machine coordinates in the display

### Panel Management

- **Add Panels**: Click "Panel" dropdown → Select panel type
- **Rearrange**: Drag panel tabs to reposition
- **Save Layout**: Layout dropdown → Save Current Layout
- **Switch Layouts**: Choose from predefined or saved layouts

## Configuration

### Serial Connection

Default settings in `src/services/SerialCommunicationService.js`:
- Baud rates: 9600 to 250000
- Data bits: 8
- Stop bits: 1
- Parity: none
- Buffer size: 4096

### Python Backend (Optional)

For advanced features, set the backend port:
```javascript
// .env
REACT_APP_PYTHON_BACKEND_PORT=8001
```

### Themes

Themes are defined in `src/App.js`. To add a custom theme:
1. Import theme from dockview
2. Add to `availableThemes` array
3. Theme CSS variables will be applied to document root

## Plugin Development

### Plugin Structure

```
plugin.zip
├── manifest.json
├── main.js
├── styles/
│   └── plugin.css
└── python/
    ├── main.py
    └── requirements.txt
```

### Manifest Example

```json
{
  "id": "my-plugin",
  "name": "My Plugin",
  "version": "1.0.0",
  "main": "main.js",
  "panel": {
    "title": "My Plugin Panel",
    "defaultLocation": "right"
  },
  "permissions": [
    "serial:read",
    "serial:write",
    "gcode:read",
    "position:subscribe"
  ],
  "pythonSupport": {
    "enabled": true,
    "entry": "python/main.py",
    "requirements": "python/requirements.txt"
  }
}
```

### Plugin API

```javascript
// main.js
const PluginPanel = ({ api }) => {
  // Subscribe to position updates
  const unsubscribe = api.position.subscribe((pos) => {
    console.log('Position:', pos);
  });
  
  // Send serial commands
  api.serial.send('G28');
  
  // Log to console
  api.console.log('Plugin loaded');
  
  return React.createElement('div', null, 'Plugin UI');
};
```

## Development

### Project Structure

```
src/
├── components/
│   ├── panels/          # Main UI panels
│   └── common/          # Shared components
├── services/
│   ├── docking/         # Panel management
│   ├── plugins/         # Plugin system
│   └── SerialCommunicationService.js
├── contexts/            # React contexts
├── utils/               # Utilities (G-code parser, etc.)
└── styles/              # CSS files
```

### Key Services

**SerialCommunicationService**: Handles all serial port communication
- Connection management
- Command queuing
- Response parsing
- Position polling

**DockingManager**: Manages dockable panel layout
- Panel creation/removal
- Layout save/load
- Predefined layouts

**GCodeParser**: Parses G-code for visualization
- Supports GRBL and standard formats
- Extracts toolpath segments
- Calculates bounds and statistics

**GCodeSender**: Manages G-code transmission
- Line-by-line sending
- Error recovery with retries
- Execution progress tracking

### Build for Production

```bash
# Create optimized build
npm run build

# Files will be in build/ directory
```

## Supported G-code Commands

### Motion
- G0/G1: Linear movement
- G2/G3: Arc movement
- G28: Home axes
- G90/G91: Absolute/relative positioning
- G92: Set position

### Machine Control
- M3/M4/M5: Spindle control
- M6: Tool change
- M30: Program end

### GRBL/FluidNC Specific
- $H: Home
- $X: Unlock
- ?: Status query
- !: Feed hold
- ~: Cycle start/resume
- Ctrl-X: Soft reset

## Troubleshooting

### Connection Issues
- Ensure browser supports WebSerial (Chrome/Edge)
- Check serial port permissions
- Verify baud rate matches controller settings
- Try disconnecting/reconnecting USB cable

### Performance
- For large G-code files (>10MB), loading may take time
- Reduce 3D visualization quality if experiencing lag
- Close unused panels to improve performance

### Plugin Errors
- Check browser console for detailed error messages
- Verify manifest.json is valid JSON
- Ensure all referenced files exist in ZIP
- Check permissions match API usage

## License

[License Type] - See LICENSE file for details
