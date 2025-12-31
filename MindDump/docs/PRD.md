# Product Requirements Document (PRD)
## MindDump - Notetaking App

**Version:** 1.0 MVP
**Platform:** iOS (Swift)
**Target Device:** iPhone 11 and newer

---

## 1. Overview

### 1.1 Purpose
MindDump is a minimalist notetaking application designed for rapid capture and organization of thoughts. The app emphasizes quick note creation through multiple input methods (typing, voice dictation) and provides a simple tag-based organization system.

### 1.2 Core Value Proposition
- Fast note capture with minimal friction
- Voice-to-text transcription for hands-free note creation
- Simple tag-based organization
- Swipe-based note prioritization for quick triage

### 1.3 Target User
Individuals who need to quickly capture thoughts, ideas, or information throughout their day without complex organizational overhead. Users who prefer a clean, distraction-free interface.

---

## 2. Features (MVP Scope)

### 2.1 Core Features (Fully Functional)

#### P0 - Note Management

| Feature | Description | User Story |
|---------|-------------|------------|
| **View Notes List** | Display all notes as cards with date, title, tags, and preview text | As a user, I can see all my notes in a scrollable list to quickly find what I need |
| **View Note Detail** | Display full note content with title, last modified date, tags, and content | As a user, I can tap a note to see its full content |
| **Create Note (Manual)** | Create notes via a blank editor with title and content fields | As a user, I can write a new note from scratch |
| **Create Note (Dialog)** | Create notes via a modal form with title, content, and tag inputs | As a user, I can quickly add a structured note with tags |
| **Promote Note** | Toggle note "promoted" status via button or swipe gesture | As a user, I can mark important notes for visibility |
| **Tag-based Filtering** | Filter notes by selecting a tag from a bottom drawer | As a user, I can view only notes with a specific tag |

#### P1 - Voice Input

| Feature | Description | User Story |
|---------|-------------|------------|
| **Voice Dictation** | Record voice and create note from transcription | As a user, I can speak my thoughts and have them saved as a note |

#### P2 - Note Prioritization

| Feature | Description | User Story |
|---------|-------------|------------|
| **Prioritize Notes (Swipe Cards)** | Tinder-style interface to swipe through notes and assign priority levels | As a user, I can quickly triage my notes by swiping right to prioritize or left to skip |
| **Priority Levels** | Four-tier priority system (Low, Medium, High, Critical) with visual color coding | As a user, I can see note importance at a glance via color |

#### P3 - Settings & Navigation

| Feature | Description | User Story |
|---------|-------------|------------|
| **Settings Screen** | View list of settings options and access prioritization | As a user, I can access app settings |
| **Navigate Back** | Return to previous screen via back button | As a user, I can navigate back from any detail view |

---

### 2.2 Intentionally Non-Functional Features (MVP)

The following features are visible in the UI but are **intentionally non-functional placeholders** in the MVP. They display an alert or perform no action when tapped.

| Feature | UI Element | MVP Behavior |
|---------|-----------|--------------|
| **Scan (Camera)** | Action Menu - Camera icon | Displays "Camera scan feature coming soon!" alert |
| **Transcribe (Link)** | Action Menu - Link icon | Same behavior as Dictate (placeholder) |
| **Show Original** | Note Actions Menu - Eye icon | Displays placeholder alert |
| **Make To-Do's** | Note Actions Menu - CheckSquare icon | Displays placeholder alert |
| **Find Related Notes** | Note Actions Menu - Search icon | Displays placeholder alert |
| **Remind Me** | Note Actions Menu - Bell icon | Displays placeholder alert |
| **Search** | Header - Search icon | No action (button present but non-functional) |
| **Account** | Settings - User icon | No action |
| **Personalize** | Settings - Palette icon | No action |
| **Privacy** | Settings - Lock icon | No action |
| **Terms of Services** | Settings - FileText icon | No action |
| **About** | Settings - Info icon | No action |
| **Feedback** | Settings - MessageSquare icon | No action |
| **Sign out** | Settings - LogOut icon | No action |
| **Get Pro** | Settings - Upgrade card | No action |

---

## 3. UI / UX Requirements

### 3.1 Navigation Structure

```
Bottom Control Bar
├── Notes List (Home)
│   ├── Note Detail
│   │   └── Note Actions Menu (overlay)
│   ├── Blank Note Editor
│   ├── Tags Drawer (bottom sheet)
│   ├── Action Menu (FAB overlay)
│   │   ├── Dictate Sheet
│   │   └── Add Note Dialog
│   └── Settings
│
└── Prioritize Notes View (Swipe Prioritization)
    └── Settings
```

**Bottom Control Bar:** Fixed navigation bar at bottom with two main pages:
- Notes (list of all notes)
- Prioritize (swipe-based prioritization interface)

### 3.2 Layout Specifications

#### 3.2.0 Bottom Control Bar (Navigation)
- **Position:** Fixed at bottom of screen above safe area
- **Height:** 60pt (including padding)
- **Background:** White with subtle top border
- **Structure:** Two-tab navigation system
  - Left tab: "Notes" with list icon
  - Right tab: "Prioritize" with priority/zap icon
- **Tab styling:**
  - Active tab: Black text with bottom border indicator
  - Inactive tab: Gray text
  - Hover state: Slight color change on inactive tabs
- **Navigation behavior:**
  - Tapping a tab switches to that page
  - Settings and note detail views overlay on top without hiding control bar
  - Maintain active tab indicator when navigating within a page (e.g., viewing note detail from Notes tab)
- **Mobile safe area:** Respects bottom safe area on devices with notch/home indicator

#### 3.2.1 Notes List View
- **Header:** Title ("All Notes" or selected tag) with dropdown indicator, Search icon, Settings icon
- **Content:** Vertically scrolling list of note cards with bottom padding (80pt) for FAB and control bar
- **FAB:** Black circular button (56pt diameter) with Plus icon, bottom-right corner (positioned above control bar)

#### 3.2.2 Note Card
- White rounded card (12pt radius)
- Date label (uppercase, gray, 12pt)
- Title (semibold, 16pt)
- Tags (badges, secondary variant)
- Preview text (gray, 14pt, max 3 lines)
- Promoted indicator (black circle with arrow-up icon, top-right)
- Swipeable horizontally to toggle promoted status

#### 3.2.3 Note Detail View
- **Header:** Back button, "All Notes" label, Search icon, Settings icon
- **Content:** Title (24pt bold), Last Modified date, Tags with chevron, Full content
- **Promote Button:** Circular toggle button (40pt diameter) with arrow-up icon
- **FAB:** Black circular button with Origami icon, opens Note Actions Menu

#### 3.2.4 Settings View
- **Header:** Back button, "Settings" title
- **Content:**
  - Upgrade card (white, rounded, contains "Get Pro" button)
  - Options list (white, rounded, icon + label rows)
- **Options:** Prioritize Notes, Account, Personalize, Privacy, Terms of Services, About, Feedback, Sign out

#### 3.2.5 Blank Note Editor
- **Header:** Back button, "New Note" label, Check (save) button
- **Content:** Title input (24pt, no border), Content textarea (full height)

#### 3.2.6 Action Menu (Overlay)
- Dark overlay (50% opacity)
- Vertical stack of action buttons (56pt circles, white) with labels (black pill badges)
- Actions: Transcribe (Link), Dictate (Mic), Scan (Camera), Write (PenLine)
- Close button replaces FAB (black circle with X)

#### 3.2.7 Note Actions Menu (Overlay)
- Same structure as Action Menu
- Actions: Show Original (Eye), Make To-Do's (CheckSquare), Find Related Notes (Search), Remind Me (Bell)

#### 3.2.8 Dictate Sheet (Bottom Sheet)
- Drag handle bar
- Title "Voice Dictation" with close button
- Large microphone button (80pt, toggles red when recording)
- Status text ("Tap to start recording" / "Recording...")
- Transcription display area (gray background)
- Cancel and Save Note buttons

#### 3.2.9 Tags Drawer (Bottom Sheet)
- Drag handle bar
- Title "Filter by Tag" with close button
- Scrollable list of tag buttons
- "All Notes" option at top
- Selected tag highlighted (black background, white text)

#### 3.2.10 Prioritize View
- **Header:** Back button (optional - can use control bar to return to Notes), "Prioritize Notes" label, Settings icon
- **Progress:** Review count and progress bar
- **Content Area:** Vertically centered with bottom padding (80pt) for control bar
- **Card:** Large swipeable card showing note details
  - Background color indicates priority level
  - Date, title, tags, preview
- **Swipe Indicators:** "PRIORITIZE" (green) / "IGNORE" (red) labels appear during swipe
- **Action Buttons:** X (ignore) and Heart (prioritize) circular buttons
- **Instructions:** Swipe guidance text above bottom control bar

### 3.3 Color System

| Token | Value | Usage |
|-------|-------|-------|
| Primary | `#030213` (near-black) | Primary buttons, FAB, text |
| Background | `#FFFFFF` | Cards, sheets |
| Background Secondary | `#F9FAFB` (gray-50) | Screen backgrounds |
| Muted | `#ECECF0` | Secondary backgrounds |
| Muted Foreground | `#717182` | Secondary text |
| Border | `rgba(0,0,0,0.1)` | Subtle borders |
| Destructive | `#D4183D` | Recording state, errors |

#### Priority Colors
| Level | Background | Description |
|-------|------------|-------------|
| None | White | Default, no priority |
| Low | Light Blue (`blue-200`) | Low priority |
| Medium | Blue (`blue-500`) | Medium priority |
| High | Purple (`purple-600`) | High priority |
| Critical | Black | Critical priority |

### 3.4 Typography

- Base font size: 16px
- Font weights: Normal (400), Medium (500)
- Headings: Medium weight
- Body: Normal weight

### 3.5 Iconography

Uses Lucide icon set:
- Plus, X, ArrowLeft, ArrowUp
- Search, Settings, MoreVertical, ChevronDown, ChevronRight
- Mic, MicOff, Camera, PenLine, Link
- User, Palette, Lock, FileText, Info, MessageSquare, LogOut, ListOrdered
- CheckSquare, Bell, Eye, Origami, Heart

### 3.6 Animations & Transitions

- Bottom sheets slide in from bottom (300ms)
- Action menus fade in with slide-up (300ms)
- Swipe cards follow finger with rotation based on drag distance
- Opacity decreases as cards are swiped away
- Spring-back animation when swipe threshold not met

### 3.7 Gestures

| Gesture | Location | Action |
|---------|----------|--------|
| Tap | Note card | Open note detail |
| Horizontal swipe | Note card | Toggle promoted status |
| Tap | Tag title (header) | Open tags drawer |
| Tap outside | Overlay/Sheet | Dismiss |
| Horizontal swipe | Prioritize card | Right = prioritize, Left = ignore |
| Drag | Prioritize card | Free movement with rotation |

---

## 4. Technical Requirements

### 4.1 Platform & Compatibility

- **Language:** Swift (latest stable version)
- **Minimum iOS:** Version compatible with iPhone 11
- **Target Devices:** iPhone 11 and newer
- **Orientation:** Portrait only

### 4.2 Development Approach

- Follow standard Apple-recommended patterns and Human Interface Guidelines
- Use native iOS components where available (UIKit or SwiftUI based on team preference)
- Local data persistence (no backend required for MVP)
- Use concrete class dependency injection (avoid protocol boilerplate)
- Implement design tokens FIRST before any UI work

### 4.3 Permissions Required

| Permission | Purpose |
|------------|---------|
| Microphone | Voice dictation feature |


### 4.5 Local Storage

- Notes persisted locally on device
- No cloud sync required for MVP
- No authentication required for MVP

### 4.6 Voice Transcription

- Use iOS Speech framework for speech-to-text
- Real-time transcription display during recording
- Save transcription as note content

### 4.7 AI Integration Points (Future Reference)

The mockup suggests potential AI integration in these areas (not implemented in MVP):
- **Show Original:** Implies notes may have AI-processed versions
- **Make To-Do's:** Suggests AI extraction of action items from note content
- **Find Related Notes:** Semantic search/similarity matching
- **Transcribe (Link):** Possibly AI-powered URL content extraction

These are noted for future consideration only. No AI dependencies or implementation in MVP.

### 4.8 Design System Setup

Create `DesignSystem/Tokens/` (Colors.swift, Spacing.swift, Typography.swift) using values from Section 3.3-3.4 as first development task. Create `Preview_DesignSystem.swift` to visualize tokens during development. Never hard-code colors/spacing/fonts in views—only reference tokens.
---

## 5. Non-Goals

### 5.1 Scope Boundaries
- No feature additions beyond what is visible in the mockup
- No cloud sync or backend services
- No user authentication or account management
- No AI or machine learning features
- No multi-device sync
- No collaboration features
- No rich text editing or formatting
- No image or file attachments (except via future Scan feature)
- No export functionality
- No widget support

### 5.2 Design Constraints
- No redesign or UX reinterpretation of the mockup
- No additional color schemes or themes beyond light mode
- No accessibility features beyond iOS system defaults
- No localization (English only)

### 5.3 Technical Constraints
- No premature optimization
- No complex architecture patterns beyond what is necessary
- No third-party dependencies unless strictly required

---

## 6. Future Suggestions

The following are observations and potential improvements for future versions, clearly separated from MVP scope:

### 6.1 Feature Completion
- Implement camera scanning for document/text capture
- Implement reminder functionality with iOS notifications
- Implement semantic search for "Find Related Notes"
- Implement AI-powered to-do extraction from notes
- Implement settings options (Account, Personalize, Privacy, etc.)
- Implement search functionality

### 6.2 Enhancements
- Dark mode support (design tokens already defined in mockup)
- Note editing (currently notes appear read-only after creation)
- Note deletion
- Tag management (create, rename, delete tags)
- Note sorting options (date, title, priority)
- iCloud sync for multi-device access
- Folders or nested organization
- Rich text or markdown support
- Share sheet integration

### 6.3 Platform Expansion
- iPad optimization
- macOS app via Catalyst or native SwiftUI
- watchOS companion for quick capture
- Widgets for home screen note preview

### 6.4 AI Integration
- Voice transcription improvements with speaker identification
- Automatic tagging based on content
- Smart note summarization
- Content-aware duplicate detection

---

## Appendix A: Screen Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         MAIN APP                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                      NOTES LIST                          │   │
│  │  [Tag Dropdown ▼]         [Search] [Settings]           │   │
│  │  ┌────────────────────────────────────────────────────┐ │   │
│  │  │ Note Card 1  ←── tap ──→ NOTE DETAIL              │ │   │
│  │  │ Note Card 2                │                       │ │   │
│  │  │ Note Card 3                ↓                       │ │   │
│  │  │ ...              [Note Actions Menu]               │ │   │
│  │  └────────────────────────────────────────────────────┘ │   │
│  │                                                          │   │
│  │  [+ FAB] ──→ ACTION MENU                               │   │
│  │               ├── Dictate ──→ DICTATE SHEET            │   │
│  │               ├── Write ──→ BLANK NOTE EDITOR          │   │
│  │               ├── Scan ──→ (placeholder)               │   │
│  │               └── Transcribe ──→ (same as dictate)     │   │
│  │                                                          │   │
│  │  [Tag Dropdown] ──→ TAGS DRAWER                        │   │
│  │                                                          │   │
│  │  [Settings] ──→ SETTINGS VIEW                          │   │
│  ├──────────────────────────────────────────────────────┤   │
│  │ [📋 Notes] │ [⚡ Prioritize]  ← BOTTOM CONTROL BAR   │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────┐   │   │
│  │                  PRIORITIZE VIEW                     │   │   │
│  │  Progress: 1/6  [████░░░░░░]                         │   │   │
│  │                                                       │   │   │
│  │           ┌──────────────────────┐                   │   │   │
│  │           │  Exploration Ideas   │                   │   │   │
│  │           │  20 APR              │                   │   │   │
│  │           │  [Design] [Prod.]    │                   │   │   │
│  │           │  Ticket App, Travel..│                   │   │   │
│  │           └──────────────────────┘                   │   │   │
│  │     [✕]              [❤]                            │   │   │
│  │   Swipe left ← or → Swipe right                     │   │   │
│  ├──────────────────────────────────────────────────────┤   │   │
│  │ [📋 Notes] │ [⚡ Prioritize]  ← BOTTOM CONTROL BAR   │   │   │
│  └──────────────────────────────────────────────────────┘   │   │
└─────────────────────────────────────────────────────────────────┘
```

**Bottom Control Bar Navigation:**
- Left tab: "Notes" - Navigate to notes list
- Right tab: "Prioritize" - Navigate to swipe-based prioritization
- Always visible to enable quick switching between pages
- Active tab shows with black text and bottom border indicator

---

## Appendix B: Component Inventory

| Component | Type | Instances |
|-----------|------|-----------|
| Bottom Control Bar | Navigation | Global (all main views) |
| Note Card | List Item | Notes List |
| Badge | Tag | Note cards, Note detail, Prioritize cards |
| FAB | Button | Notes List, Note Detail |
| Bottom Sheet | Modal | Tags Drawer, Dictate Sheet |
| Overlay Menu | Modal | Action Menu, Note Actions Menu |
| Header | Navigation | All views |
| Text Input | Form | Blank Note Editor, Add Note Dialog |
| Text Area | Form | Blank Note Editor, Add Note Dialog |
| Icon Button | Button | Headers, Cards, Menus |
| Settings Row | List Item | Settings View |
| Progress Bar | Indicator | Prioritize View |
| Swipeable Card | Interactive | Prioritize View |

---

*Document generated from Figma mockup analysis. Implementation should strictly follow mockup specifications.*
