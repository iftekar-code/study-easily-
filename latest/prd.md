StudyEasily - Detailed Development Prompt
Project Overview
Create a full-stack web application called StudyEasily - an educational platform designed for students to manage their study materials and transform assignments into professional infographics.

Technology Stack
Frontend

Framework: React 18+
Build Tool: Vite
State Management: React Context API / useState hooks
Routing: React Router v6
Styling: CSS Modules / Tailwind CSS / Styled Components (choose one)
HTTP Client: Supabase client library

Backend

BaaS Platform: Supabase (already connected,project name-webapp)

PostgreSQL database
Authentication service
Storage for images
Real-time subscriptions
Row-Level Security (RLS)



Deployment

Hosting: Netlify
CI/CD: Automatic deployment from Git repository
Domain: Custom domain support
SSL: Automatic HTTPS

Additional Tools

Image Processing: Canvas API / Fabric.js / Konva.js for infographic generation
Icons: React Icons / Lucide React
Notifications: React Toastify / Sonner
File Upload: React Dropzone


Core Features Specification
1. User Authentication System
Step 1: Login/Signup Screen
Required Fields:

Full Name (text input, required)
Email Address (email input, required, validated)

Authentication Methods:

Email Magic Link (Passwordless)

User enters full name and email
System sends a one-time magic link to the provided email
Link expires after 1 hour
Single-use link (invalidated after first click)
On click, user is authenticated and redirected to home page
No password creation or storage required


Google OAuth 2.0

"Continue with Google" button prominently displayed
Opens Google authentication popup
User selects Google account
Automatically retrieves name and email from Google profile
On success, redirect to home page
JWT token stored for session management



Step 2: Post-Authentication Flow

No password required at any point in the user journey
Upon successful authentication (either method):

Store user session using Supabase JWT tokens
Redirect directly to /home route
Display welcome message with user's name
Maintain session across browser refreshes
Auto-logout after 30 days of inactivity (configurable)



Security Requirements

Implement Supabase Row-Level Security (RLS) policies
All user data isolated by user_id
Secure HTTP-only cookies for session management
CSRF protection enabled
Rate limiting on authentication endpoints (prevent brute force)
Email verification for magic links
OAuth state parameter for Google authentication


2. Layout & Navigation System
Mobile Layout (< 768px breakpoint)
Bottom Navigation Bar:

Fixed position at bottom of screen
Always visible (z-index: 100)
Height: 64px
Background: White (light mode) / Dark Gray #1A1A1A (dark mode)
Box shadow: 0 -2px 10px rgba(0,0,0,0.1)

Navigation Items:

Home

Icon: Home icon
Label: "Home"
Route: /home
Active state: Purple fill #7B2CBF


Settings

Icon: Settings/Gear icon
Label: "Settings"
Route: /settings
Active state: Purple fill #7B2CBF



Behavior:

Smooth transitions between tabs (200ms ease-in-out)
Active tab highlighted with primary purple color
Haptic feedback on tap (mobile devices)
Icons scale slightly on press (transform: scale(0.95))

Desktop Layout (≥ 768px breakpoint)
Sidebar Navigation:

Fixed position on left side
Width: 240px (expanded) / 80px (collapsed)
Full height of viewport
Background: White (light mode) / #1A1A1A (dark mode)
Border right: 1px solid #E0AAFF

Features:

Collapsible/expandable toggle button at top
Logo/app name at the top (StudyEasily)
Navigation items vertically stacked
Icons + text labels (when expanded)
Icons only (when collapsed)
Smooth expand/collapse animation (300ms)
Active item: Purple background #7B2CBF with white text
Hover state: Light purple background #E0AAFF

Navigation Items (same as mobile):

Home (with home icon)
Settings (with settings icon)


3. Home Screen - Main Dashboard
The home screen is divided into two primary sections displayed as cards or tabs:
Section A: Notes Manager
Purpose:
Text-based note-taking and organization system for students
Features:

Create Notes

Large "+" floating action button (bottom right on mobile, top right on desktop)
Click opens modal/new page with:

Note title input (required)
Note content textarea (rich text editor optional)
Subject/category dropdown (optional)
Save button (primary purple)
Cancel button (secondary)


Auto-save draft every 30 seconds
Character count display
Timestamps: created_at, updated_at


View Notes

Grid layout (mobile: 1 column, tablet: 2 columns, desktop: 3 columns)
Each note displayed as a card:

Note title (bold, 18px)
Truncated preview (first 100 characters)
Timestamp (relative: "2 hours ago")
Subject badge (if categorized)


Click card to expand and view full content


Search & Filter

Search bar at top of notes section
Real-time filtering as user types
Search across title and content
Debounced search (300ms delay)
No results state with illustration


Edit Notes

Click on any note card to open in edit mode
Same interface as create note
Save changes button
"Last edited" timestamp updates


Delete Notes

Swipe left gesture on mobile reveals delete button
Three-dot menu on desktop with delete option
Confirmation modal: "Are you sure you want to delete this note?"
Soft delete with 30-day recovery period (optional)


Additional Features

Sort options: Recent, Alphabetical, Category
Empty state: "No notes yet. Create your first note!"
Loading skeleton while fetching notes
Infinite scroll or pagination for large note collections
Export note as PDF/TXT (future enhancement)



Section B: Homework & Assignments (Infographic Generator)
Purpose:
Transform assignment images into professionally designed visual infographics
Upload Interface:

Drag-and-Drop Zone

Large dashed border rectangle
Purple border color #7B2CBF
Icon: Upload cloud icon
Text: "Drag and drop your assignment image here"
Text: "or click to browse"
Supported formats displayed: JPG, PNG, PDF
Max file size: 10MB
Hover state: Background changes to light purple #E0AAFF


File Validation

Check file type (accept only JPG, PNG, PDF)
Check file size (max 10MB)
Display error toast if validation fails
Show file name and size after successful upload



Processing Flow:

Image Preview

Display uploaded image thumbnail
Edit button to re-upload
"Generate Infographic" button (primary purple, large)


Generation Process

Click "Generate Infographic" button
Show loading spinner with message: "Creating your infographic..."
Progress bar (optional): 0% → 100%
Estimated time: 5-10 seconds


Infographic Generation Algorithm
Option A: Template-Based Approach

Analyze uploaded image using OCR (Tesseract.js)
Extract text content from image
Parse and structure content (headings, bullet points, key facts)
Apply pre-designed template with:

StudyEasily branding
Purple and white color scheme
Icons and visual elements
Proper spacing and typography
Professional layout (grid-based)


Generate final infographic image (PNG/JPG)

Option B: AI-Powered Approach

Send image to AI API (e.g., OpenAI GPT-4 Vision, Claude Vision)
Request: "Analyze this assignment and create a structured outline"
Use Canvas API to render infographic with AI-generated layout
Apply custom styling and branding

Option C: Canvas-Based Manual Generation

Use Fabric.js or Konva.js
Pre-built templates stored in database
User selects template style
Auto-populate with extracted content
Allow customization (colors, fonts, layout)


Display Generated Infographic

Show final infographic in preview mode
High-quality rendering
"Download" button (download as PNG, 1920x1080px)
"Share" button (future: social media integration)
"Regenerate" button to create new version
"Save to Gallery" button


Infographic Gallery

Grid view of all previously generated infographics
Thumbnail previews
Click to view full size
Download and share options
Delete option with confirmation
Sort by: Most Recent, Oldest



Technical Implementation Details:
javascript// Example: Infographic Generation Flow

const generateInfographic = async (imageFile) => {
  // 1. Upload image to Supabase Storage
  const { data: uploadData, error: uploadError } = await supabase
    .storage
    .from('assignment-images')
    .upload(`${userId}/${Date.now()}_${imageFile.name}`, imageFile);

  if (uploadError) throw uploadError;

  // 2. Get public URL
  const { data: { publicUrl } } = supabase
    .storage
    .from('assignment-images')
    .getPublicUrl(uploadData.path);

  // 3. Process image (OCR or AI analysis)
  const extractedContent = await analyzeImage(publicUrl);

  // 4. Generate infographic using Canvas
  const infographicBlob = await createInfographic(extractedContent);

  // 5. Upload generated infographic to Supabase
  const { data: infographicData } = await supabase
    .storage
    .from('infographics')
    .upload(`${userId}/${Date.now()}_infographic.png`, infographicBlob);

  // 6. Save metadata to database
  await supabase
    .from('infographics')
    .insert({
      user_id: userId,
      original_image_url: publicUrl,
      generated_infographic_url: infographicData.publicUrl,
      created_at: new Date().toISOString()
    });

  return infographicData.publicUrl;
};

4. Settings Screen
Layout:
Vertical list of settings options with dividers
Theme Toggle (Dark/Light Mode)

Display: Toggle switch component
Label: "Dark Mode" or "Light Mode"
Icon: Sun (light mode) / Moon (dark mode)
Behavior:

Toggle between light and dark theme
Smooth transition (200ms) for all color changes
Persist preference in localStorage
Apply theme to entire application
Default: System preference detection



Color Scheme:
Light Mode:

Background: #FFFFFF
Card background: #F8F9FA
Text: #1A1A1A
Borders: #E0AAFF

Dark Mode:

Background: #1A1A1A
Card background: #2D2D2D
Text: #FFFFFF
Borders: #7B2CBF

Logout

Display: Button with logout icon
Label: "Logout"
Color: Red/Danger color for emphasis
Behavior:

Click triggers confirmation modal
Modal text: "Are you sure you want to logout?"
Confirm button: "Logout" (red)
Cancel button: "Stay Logged In"
On confirm:

Clear Supabase session
Clear localStorage
Redirect to login page
Show toast: "Successfully logged out"





About App

Display: Card or expandable section
Icon: Info icon
Contents:

App name: StudyEasily
Version number: v1.0.0
Short description: "Your companion for efficient studying and beautiful assignments"
Developer information
Links:

Terms of Service
Privacy Policy
Contact Support (email)
GitHub repository (if open source)


"Made with ❤️ for students" footer
Copyright notice



Additional Settings (Future Enhancements)

Notification preferences
Account management
Data export
Language selection
Font size adjustment
Storage usage display


UI/UX Design System
Color Palette
Primary Colors:
css--primary-purple: #7B2CBF;
--secondary-purple: #9D4EDD;
--light-purple: #C77DFF;
--pale-purple: #E0AAFF;
--extra-pale-purple: #F3E8FF;
Neutral Colors:
css/* Light Mode */
--bg-light: #FFFFFF;
--card-light: #F8F9FA;
--text-light: #1A1A1A;
--text-secondary-light: #6C757D;
--border-light: #E0AAFF;

/* Dark Mode */
--bg-dark: #1A1A1A;
--card-dark: #2D2D2D;
--text-dark: #FFFFFF;
--text-secondary-dark: #B8B8B8;
--border-dark: #7B2CBF;
Semantic Colors:
css--success: #28A745;
--error: #DC3545;
--warning: #FFC107;
--info: #17A2B8;
Typography
Font Family:
cssfont-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', 
             'Roboto', 'Oxygen', 'Ubuntu', 'Cantarell', sans-serif;
Font Sizes:
css--h1: 32px;      /* Page titles */
--h2: 24px;      /* Section headings */
--h3: 20px;      /* Card titles */
--body: 16px;    /* Regular text */
--small: 14px;   /* Secondary text */
--tiny: 12px;    /* Captions, timestamps */
Font Weights:
css--regular: 400;
--medium: 500;
--semibold: 600;
--bold: 700;
Spacing System (8px grid)
css--space-xs: 4px;
--space-sm: 8px;
--space-md: 16px;
--space-lg: 24px;
--space-xl: 32px;
--space-2xl: 48px;
Border Radius
css--radius-sm: 4px;    /* Small elements */
--radius-md: 8px;    /* Buttons, inputs */
--radius-lg: 12px;   /* Cards */
--radius-xl: 16px;   /* Modals */
--radius-round: 50%; /* Icons, avatars */
Shadows
css--shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
--shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
--shadow-lg: 0 10px 25px rgba(0, 0, 0, 0.15);
--shadow-xl: 0 20px 40px rgba(0, 0, 0, 0.2);
Component Styles
Buttons
Primary Button:
cssbackground: var(--primary-purple);
color: #FFFFFF;
padding: 12px 24px;
border-radius: var(--radius-md);
font-weight: var(--semibold);
transition: all 200ms ease-in-out;

&:hover {
  background: var(--secondary-purple);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

&:active {
  transform: translateY(0);
}
Secondary Button:
cssbackground: transparent;
color: var(--primary-purple);
border: 2px solid var(--primary-purple);
padding: 12px 24px;
border-radius: var(--radius-md);
font-weight: var(--semibold);

&:hover {
  background: var(--pale-purple);
}
Input Fields
cssborder: 1px solid var(--border-light);
border-radius: var(--radius-md);
padding: 12px 16px;
font-size: var(--body);
transition: all 200ms;

&:focus {
  outline: none;
  border-color: var(--primary-purple);
  box-shadow: 0 0 0 3px rgba(123, 44, 191, 0.1);
}

&::placeholder {
  color: var(--text-secondary-light);
}
Cards
cssbackground: var(--card-light);
border-radius: var(--radius-lg);
padding: var(--space-lg);
box-shadow: var(--shadow-sm);
transition: all 200ms;

&:hover {
  box-shadow: var(--shadow-md);
  transform: translateY(-4px);
}
Animations & Transitions
Page Transitions:
css.page-enter {
  opacity: 0;
  transform: translateY(20px);
}

.page-enter-active {
  opacity: 1;
  transform: translateY(0);
  transition: all 300ms ease-out;
}

.page-exit {
  opacity: 1;
}

.page-exit-active {
  opacity: 0;
  transition: all 200ms ease-in;
}
Loading States:

Skeleton loaders for content
Spinner for processing states
Progress bars for uploads
Smooth fade-in for loaded content

Responsive Breakpoints
css--mobile: 320px;
--tablet: 768px;
--desktop: 1024px;
--large-desktop: 1440px;
Mobile-First Approach:
All components designed for mobile first, then enhanced for larger screens.
Accessibility Requirements

WCAG 2.1 AA Compliance

Color contrast ratio ≥ 4.5:1 for text
Color contrast ratio ≥ 3:1 for UI components
All interactive elements keyboard accessible
Focus indicators visible


Semantic HTML

Proper heading hierarchy (h1, h2, h3)
ARIA labels for icons
Alt text for images
Form labels properly associated


Keyboard Navigation

Tab order logical and intuitive
Enter/Space to activate buttons
Escape to close modals
Arrow keys for navigation menus


Screen Reader Support

Meaningful ARIA labels
Live regions for dynamic content
Descriptive link text
Form error announcements




Database Schema (Supabase)
Table: users (managed by Supabase Auth)
sqlCREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email TEXT UNIQUE NOT NULL,
  full_name TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
Table: notes
sqlCREATE TABLE notes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  content TEXT,
  category TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Index for faster queries
CREATE INDEX idx_notes_user_id ON notes(user_id);
CREATE INDEX idx_notes_created_at ON notes(created_at DESC);
Table: infographics
sqlCREATE TABLE infographics (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  original_image_url TEXT NOT NULL,
  generated_infographic_url TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Index for faster queries
CREATE INDEX idx_infographics_user_id ON infographics(user_id);
CREATE INDEX idx_infographics_created_at ON infographics(created_at DESC);
Row-Level Security (RLS) Policies
sql-- Enable RLS
ALTER TABLE notes ENABLE ROW LEVEL SECURITY;
ALTER TABLE infographics ENABLE ROW LEVEL SECURITY;

-- Notes policies
CREATE POLICY "Users can view their own notes" 
  ON notes FOR SELECT 
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create their own notes" 
  ON notes FOR INSERT 
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own notes" 
  ON notes FOR UPDATE 
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete their own notes" 
  ON notes FOR DELETE 
  USING (auth.uid() = user_id);

-- Infographics policies
CREATE POLICY "Users can view their own infographics" 
  ON infographics FOR SELECT 
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create their own infographics" 
  ON infographics FOR INSERT 
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete their own infographics" 
  ON infographics FOR DELETE 
  USING (auth.uid() = user_id);
Storage Buckets
sql-- Create storage buckets
INSERT INTO storage.buckets (id, name, public) VALUES 
  ('assignment-images', 'assignment-images', false),
  ('infographics', 'infographics', true);

-- Storage policies for assignment-images
CREATE POLICY "Users can upload their assignment images"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'assignment-images' AND 
    auth.uid()::text = (storage.foldername(name))[1]
  );

CREATE POLICY "Users can view their assignment images"
  ON storage.objects FOR SELECT
  USING (
    bucket_id = 'assignment-images' AND 
    auth.uid()::text = (storage.foldername(name))[1]
  );

-- Storage policies for infographics (public)
CREATE POLICY "Anyone can view infographics"
  ON storage.objects FOR SELECT
  USING (bucket_id = 'infographics');

CREATE POLICY "Users can upload their infographics"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'infographics' AND 
    auth.uid()::text = (storage.foldername(name))[1]
  );

Project Structure
studyeasily/
├── public/
│   ├── favicon.ico
│   └── logo.png
├── src/
│   ├── assets/
│   │   ├── icons/
│   │   └── images/
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.jsx
│   │   │   ├── Card.jsx
│   │   │   ├── Input.jsx
│   │   │   ├── Modal.jsx
│   │   │   └── Loader.jsx
│   │   ├── layout/
│   │   │   ├── BottomNav.jsx
│   │   │   ├── Sidebar.jsx
│   │   │   └── Header.jsx
│   │   ├── notes/
│   │   │   ├── NoteCard.jsx
│   │   │   ├── NoteEditor.jsx
│   │   │   ├── NoteList.jsx
│   │   │   └── SearchBar.jsx
│   │   ├── infographics/
│   │   │   ├── UploadZone.jsx
│   │   │   ├── InfographicPreview.jsx
│   │   │   ├── InfographicGallery.jsx
│   │   │   └── GenerationProgress.jsx
│   │   └── auth/
│   │       ├── LoginForm.jsx
│   │       ├── GoogleButton.jsx
│   │       └── MagicLinkSent.jsx
│   ├── pages/
│   │   ├── Auth.jsx
│   │   ├── Home.jsx
│   │   └── Settings.jsx
│   ├── contexts/
│   │   ├── AuthContext.jsx
│   │   └── ThemeContext.jsx
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useNotes.js
│   │   ├── useInfographics.js
│   │   └── useTheme.js
│   ├── services/
│   │   ├── supabase.js
│   │   ├── auth.service.js
│   │   ├── notes.service.js
│   │   ├── infographics.service.js
│   │   └── storage.service.js
│   ├── utils/
│   │   ├── constants.js
│   │   ├── helpers.js
│   │   └── validators.js
│   ├── styles/
│   │   ├── global.css
│   │   ├── variables.css
│   │   └── themes.css
│   ├── App.jsx
│   ├── main.jsx
│   └── router.jsx
├── .env.example
├── .gitignore
├── package.json
├── vite.config.js
├── README.md
└── netlify.toml

Environment Variables
Create a .env file:
bash# Supabase Configuration
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key

# Optional: Image Processing API (if using external service)
VITE_IMAGE_API_KEY=your_image_api_key

# Optional: Analytics
VITE_ANALYTICS_ID=your_analytics_id

Development Workflow
Phase 1: Setup & Authentication (Week 1)

 Initialize React + Vite project
 Set up Supabase project and configure
 Implement authentication UI (login/signup)
 Integrate magic link authentication
 Integrate Google OAuth
 Create protected route wrapper
 Set up routing (React Router)
 Implement theme context (dark/light mode)

Phase 2: Core Features - Notes (Week 2)

 Design and create database schema for notes
 Implement RLS policies
 Create notes CRUD operations
 Build note creation UI
 Build note list/grid UI
 Implement search functionality
 Add edit and delete features
 Add loading and error states

Phase 3: Core Features - Infographic Generator (Weeks 3-4)

 Research and choose infographic generation approach
 Set up Supabase Storage buckets
 Build file upload interface (drag-and-drop)
 Implement image validation
 Develop infographic generation logic
 Create infographic preview UI
 Add download functionality
 Build infographic gallery
 Implement delete infographic feature

Phase 4: Layout & Navigation (Week 5)

 Build responsive bottom navigation (mobile)
 Build sidebar navigation (desktop)
 Implement navigation state management
 Add smooth transitions between pages
 Create responsive breakpoints
 Test on multiple devices

Phase 5: Settings & Polish (Week 6)

 Build settings page layout
 Implement theme toggle (dark/light mode)
 Create logout functionality with confirmation
 Build "About App" section
 Add toast notifications
 Implement error boundaries
 Add loading skeletons
 Performance optimization

Phase 6: Testing & Deployment (Week 7)

 Unit testing (Vitest)
 Integration testing
 E2E testing (Playwright/Cypress)
 Cross-browser testing
 Mobile responsiveness testing
 Accessibility audit (WAVE, axe DevTools)
 Performance audit (Lighthouse)
 Set up Netlify deployment
 Configure custom domain
 Set up CI/CD pipeline
 Production deployment


Performance Requirements
Target Metrics

First Contentful Paint (FCP): < 1.5s
Time to Interactive (TTI): < 3.5s
Largest Contentful Paint (LCP): < 2.5s
Cumulative Layout Shift (CLS): < 0.1
Lighthouse Score: > 90

Optimization Strategies

Code splitting with React.lazy()
Image optimization (WebP format, lazy loading)
CDN for static assets
Minification and compression
Caching strategies
Database query optimization
Bundle size monitoring


Security Checklist

 Environment variables never exposed in client code
 Supabase RLS policies thoroughly tested
 Input validation on all forms
 XSS protection (sanitize user inputs)
 CSRF protection enabled
 Rate limiting on API endpoints
 Secure HTTP-only cookies
 Regular dependency updates
 Security headers configured (Netlify)
 No sensitive data in localStorage


Success Criteria
Functional Requirements

✅ Users can sign up/login without password
✅ Users can create, read, update, delete notes
✅ Users can search through their notes
✅ Users can upload assignment images
✅ System generates professional infographics
✅ Users can download infographics
✅ Users can switch between light/dark themes
✅ Users can logout securely
✅ Responsive on mobile, tablet, desktop

Non-Functional Requirements

✅ Application loads in < 3 seconds
✅ Infographic generation completes in < 10 seconds
✅ 99.9% uptime (Netlify SLA)
✅ Mobile-friendly (Google Mobile-Friendly Test)
✅ Accessible (WCAG 2.1 AA)
✅ Secure (no major vulnerabilities)


Future Enhancements (v2.0)

Note Organization

Folders and categories
Tags system
Color coding
Favorites/starred notes


Collaboration

Share notes with classmates
Real-time collaborative editing
Comments on notes


Advanced Infographics

Multiple template options
Customizable colors and fonts
AI-powered content suggestions
Video assignments support


Study Tools

Flashcards from notes
Quiz generation
Study reminders/notifications
Pomodoro timer


Mobile App

React Native version
Offline mode
Push notifications


Integrations

Google Drive sync
Notion integration
Calendar integration
Export to PDF/Word




Support & Maintenance
Monitoring

Application performance monitoring (APM)
Error tracking (Sentry)
User analytics (Google Analytics / Plausible)
Uptime monitoring

Backup Strategy

Daily database backups (Supabase automatic)
Weekly full system backups
30-day retention period

Update Schedule

Security patches: Immediate
Bug fixes: Weekly
Feature updates: Monthly
Major releases: Quarterly


Conclusion
This detailed prompt provides comprehensive specifications for building StudyEasily - a modern, student-focused web application with note-taking and infographic generation capabilities. Follow the phased development workflow, adhere to the design system, and ensure all security and performance requirements are met.
Key Differentiators:

Passwordless authentication for seamless access
AI-powered infographic generation
Professional purple and white design
Mobile-first responsive design
Student-centric features

Tech Stack Summary:

Frontend: React + Vite
Backend: Supabase
Deployment: Netlify
Styling: Purple (#7B2CBF) and White theme

Build with modern best practices, focus on user experience, and create a tool that genuinely helps students succeed in their academic journey