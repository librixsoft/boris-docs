# Boris AI Interface Documentation

## Overview

Boris AI is a local model panel that provides a clean, intuitive interface for managing and interacting with local AI models. The application features a modern sidebar navigation with three main sections: Chat, Models, and Settings.

## Main Interface Components

### Sidebar Navigation
- **Boris AI Logo**: Icon with application branding
- **Navigation Menu**: Three main sections accessible via buttons
  - **Chat**: Interactive conversation interface
  - **Models**: Model management and configuration
  - **Settings**: Application configuration

### Status Information
The sidebar footer displays real-time system status:
- **GPU Information**: GPU name and VRAM when available
- **GPU Memory**: Free GPU memory
- **RAM GPU Usage**: Memory utilization statistics
- **Connection Status**: Online/Offline indicator

## Chat Interface

### Welcome Screen
- Personalized greeting when no models are loaded
- Quick action suggestions for starting conversations
- Clean, minimal design for focused interaction

### Chat Features
- **Message History**: Scrollable conversation display
- **Token Tracking**: Real-time display of input/output tokens and context usage
- **Markdown Support**: Rich text rendering for AI responses
- **Typing Indicator**: Visual feedback during AI responses
- **Message Controls**: New conversation, send, and stop functionality

### Input Controls
- **New Chat Button**: Start fresh conversations
- **Message Input**: Auto-resizing textarea with keyboard shortcuts
- **Send Button**: Submit messages (Enter key support)
- **Stop Button**: Interrupt ongoing AI responses

## Models Interface

### Phase Candidates Management
The interface manages two types of model phases:

#### Planning Phase
- **Immediate Loading**: Models loaded directly into VRAM
- **Configuration Display**: Context size, GPU layers, temperature
- **VRAM Status**: Visual indicators for loaded models
- **Eject Function**: Unload models from memory

#### Writing Phase
- **On-Demand Loading**: Models load during execution
- **Candidate Selection**: Choose models for writing tasks
- **Configuration Parameters**: Same settings as planning phase
- **Memory Management**: Efficient VRAM utilization

### Available Models Section
- **Model Cards**: Display detected models with metadata
- **Configuration Panel**: Advanced parameter settings
  - Context Size (512-32768)
  - Threads (1-32)
  - GPU Layers (0-99)
  - Batch Size (1-2048)
  - Temperature (0.0-2.0)
  - Max Tokens (1-32768)
- **Phase Assignment**: Radio buttons for planning/writing selection
- **Load Actions**: Context-aware loading buttons

### Model Status Indicators
- **Available**: Ready to load
- **Loaded**: Currently in VRAM
- **Loading**: In-progress status with spinner
- **Configuration**: Expanded parameter panels

## Settings Interface

### Application Configuration
- **Llama Server Path**: Path to the llama-server executable
- **Models Directory**: Location for model files
- **Workspace Prefix**: Default workspace directory
- **Server Version**: Automatic version detection

### Path Management
- **Browse Buttons**: File system navigation
- **Relative/Absolute Display**: Both path formats shown
- **Validation**: Path verification and error handling

### Installation Management
- **Download Button**: Automatic llama-server installation
- **Status Indicators**: Installation progress and completion
- **Version Control**: Core version management

## Design Features

### Responsive Layout
- **Adaptive Design**: Works across different screen sizes
- **Collapsible Sections**: Efficient space utilization
- **Status Indicators**: Real-time feedback

### User Experience
- **Loading States**: Visual feedback for all operations
- **Error Handling**: Clear error messages and recovery options
- **Keyboard Shortcuts**: Efficient navigation and interaction
- **Contextual Actions**: Relevant buttons based on current state

### Visual Design
- **Modern UI**: Clean, professional interface
- **Status Chips**: Color-coded information display
- **Progress Indicators**: Loading spinners and progress bars
- **Consistent Styling**: Unified design language throughout

## Technical Implementation

### Frontend Technologies
- **Vue.js**: Reactive component framework
- **CSS Grid/Flexbox**: Responsive layout system
- **SVG Icons**: Scalable vector graphics
- **Real-time Updates**: Live data synchronization

### Performance Features
- **Lazy Loading**: On-demand model loading
- **Memory Management**: Efficient VRAM usage
- **Background Operations**: Non-blocking UI operations
- **Status Monitoring**: Continuous system health checks

This interface provides a comprehensive yet user-friendly environment for managing local AI models, with emphasis on performance, usability, and real-time feedback.
