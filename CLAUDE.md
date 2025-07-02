# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an iOS SwiftUI application called "pyqfenxi" (朋友圈分析 - "Friend Circle Analysis") that analyzes WeChat Moments screenshots using Doubao AI to provide dating advice and personality insights.

**Key Technologies:**
- SwiftUI with UIKit interoperability
- Swift 5.0, iOS 18.2+
- Doubao (豆包) AI API integration for multimodal analysis
- Modern Swift concurrency (async/await)

## Development Commands

### Building and Running
```bash
# Open in Xcode
open pyqfenxi/pyqfenxi.xcodeproj

# Build from command line
xcodebuild -project pyqfenxi/pyqfenxi.xcodeproj -scheme pyqfenxi -destination "platform=iOS Simulator,name=iPhone 15" build

# Run tests from command line
xcodebuild test -project pyqfenxi/pyqfenxi.xcodeproj -scheme pyqfenxi -destination "platform=iOS Simulator,name=iPhone 15"
```

### Testing Framework
- **Unit Tests**: Uses Swift Testing framework (`import Testing`) with `@Test` annotations
- **UI Tests**: Uses XCTest framework
- Test files: `pyqfenxiTests/` and `pyqfenxiUITests/`
- Run single test: Use Xcode test navigator or `xcodebuild test -only-testing:pyqfenxiTests/TestName`

## Architecture & Code Structure

### Core Components

**APIService.swift** - AI Integration Layer
- Singleton pattern (`APIService.shared`)
- Integrates with Doubao API at `https://ark.cn-beijing.volces.com/api/v3/chat/completions`
- Uses model `doubao-seed-1-6-thinking-250615`
- Handles image compression and Base64 encoding
- Comprehensive error handling with custom `APIError` enum
- API key from environment variable `DOUBAO_API_KEY` or fallback

**DataModels.swift** - Data & State Management
- `AnalysisManager`: Main `@ObservableObject` for app state with premium integration
- `AnalysisResult`: Basic analysis data structure
- `DetailedAnalysisResult`: Comprehensive analysis with strategy phases
- `PremiumFeatureInfo` & `AnalysisComparison`: Premium feature comparison data
- All models are `Codable` for JSON serialization
- History tracking with 20-item limit

**ContentView.swift** - SwiftUI Interface
- Tab-based interface: Photo Selection, Analysis Results, Settings
- Uses `PhotosPicker` for image selection (5-20 images)
- Grid layout for image display
- Integrated with `AnalysisManager` for state updates
- Premium upgrade cards and payment flow integration
- Debug premium controls in Settings (DEBUG mode only)

**PremiumManager.swift** - Premium Feature Management
- Singleton pattern for premium status management
- Free trial system (one-time detailed analysis)
- Simulated purchase flow with 95% success rate
- Local persistence with UserDefaults
- Support for monthly, yearly, and lifetime plans
- Debug tools for testing premium features

**PremiumViews.swift** - Premium UI Components
- `PremiumUpgradeCard`: Promotes premium features in basic analysis results
- `PremiumUpgradeSheet`: Full premium subscription interface
- Feature comparison tables and plan selection cards
- Gradient designs emphasizing premium value proposition

### API Integration Patterns

**Image Processing Pipeline:**
1. Image selection via PhotosPicker
2. Resize to max 1024x1024 pixels
3. JPEG compression at 0.5 quality
4. Base64 encoding for API transmission
5. Multimodal prompt with text + images

**Error Handling Strategy:**
- Network timeouts: 2-minute request, 3-minute resource
- Custom error messages in Chinese
- Graceful degradation for API failures
- Debug logging for troubleshooting

### State Management

The app uses SwiftUI's `@StateObject` and `@ObservableObject` pattern:
- `AnalysisManager` centralizes all app state
- `@Published` properties trigger UI updates
- Async operations use `MainActor.run` for UI updates
- History persistence in memory (no Core Data)

## Development Guidelines

### Code Conventions
- Chinese comments and UI text
- Comprehensive error messages in Chinese
- Use of modern Swift concurrency
- Singleton pattern for services
- Codable for all data models

### API Configuration
- API key via environment variable `DOUBAO_API_KEY`
- Model: `doubao-seed-1-6-thinking-250615`
- Image optimization: 1024x1024 max, 0.5 JPEG quality
- Request timeout: 120 seconds
- Resource timeout: 180 seconds
- Enhanced prompts for detailed analysis with professional relationship coaching expertise

### Premium Features Integration
- **Free Trial**: First-time users get one free detailed analysis
- **Premium Plans**: Monthly (¥29.9), Yearly (¥199.9), Lifetime (¥699.9)
- **Feature Gating**: `PremiumManager.shared.hasAccess(to: .detailedAnalysis)`
- **Simulated Purchases**: 95% success rate for testing, 2-second delay
- **Local Storage**: Premium status persisted with UserDefaults
- **Debug Controls**: Toggle premium status in Settings (DEBUG builds only)

### Testing Approach
- Minimal test coverage currently (placeholder tests)
- Use Swift Testing framework for new unit tests
- XCTest for UI automation tests
- Focus on API integration and data model validation

## Project Configuration

**Bundle ID:** `me.superhuang.pyqfenxi`
**Team ID:** DNPNKUM3NP
**Deployment Target:** iOS 18.2+
**Supported Devices:** iPhone and iPad
**Code Signing:** Automatic

### Dependencies
- No external package dependencies
- Pure SwiftUI/UIKit implementation
- No CocoaPods or Swift Package Manager usage

### File Structure
```
pyqfenxi/
├── pyqfenxi.xcodeproj/     # Xcode project
├── pyqfenxi/               # Main source code
│   ├── pyqfenxiApp.swift   # App entry point
│   ├── ContentView.swift   # Main UI
│   ├── APIService.swift    # AI integration
│   ├── DataModels.swift    # Data structures
│   └── Assets.xcassets/    # App assets
├── pyqfenxiTests/          # Unit tests (Swift Testing)
└── pyqfenxiUITests/        # UI tests (XCTest)
```

## Troubleshooting

**Common Issues:**
- API timeouts: Check network connectivity and API key
- Image processing errors: Verify image format and size
- JSON parsing failures: Check API response format
- Build errors: Ensure iOS 18.2+ deployment target

**Debug Information:**
- API request size and image count logged to console
- Response status codes and error details printed
- Image compression sizes tracked during processing