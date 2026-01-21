DRIFTWISE
Product Requirements Document
AI-Powered Serendipitous Local History Companion

Version 1.0
January 2026
Prepared for: DreamLab

Executive Summary
Driftwise is a voice-first Progressive Web Application (PWA) that transforms routine car journeys into opportunities for discovery. By continuously monitoring GPS position and leveraging Google’s Gemini Live API with web search grounding, Driftwise surfaces fascinating, edge-case historical facts about locations the driver passes through—delivered naturally via voice, designed to interrupt audio playback like a navigation prompt would.
The application prioritises unusual, memorable facts over generic descriptions, ensuring each snippet adds genuine value to the journey. A configurable interest threshold and voice-controlled cadence allow drivers to tune the experience to their preferences and the richness of the area they’re traversing.

Target Platform: Android (PWA) with Android Auto audio integration
Primary Model: Gemini Live API (gemini-2.5-flash-native-audio-preview)
Geocoding: Nominatim / OpenStreetMap (free, open-source)
Initial Scope: Personal use, architected for future scalability
2. Problem Statement
2.1 The Opportunity
Drivers spend significant time on familiar routes, passing through places rich with history they never learn about. Traditional audio guides require pre-planning and focus on tourist hotspots. There is no solution that proactively surfaces surprising, locally-relevant historical context in real-time during everyday journeys.
2.2 Pain Points Addressed
Missed opportunities: Interesting history exists everywhere, but discovering it requires active research.
Generic content: Existing solutions repeat common facts (“known for stone cottages”) rather than surfacing unique stories.
Interaction friction: Looking up information while driving is dangerous and impractical.
Repetitive audio: On regular commutes, any static content quickly becomes stale.
3. Solution Overview
3.1 Core Concept
Driftwise operates as an ambient, voice-driven companion that:
Polls GPS every 5 minutes (configurable) to establish current location.
Reverse-geocodes coordinates to obtain place names within a 1-2km radius.
Queries Gemini with web search grounding to research local history.
Filters for edge-case, non-obvious facts using a carefully crafted system prompt.
Opens a Gemini Live API session to deliver the fact via natural speech.
Listens for 5 seconds post-delivery for follow-up questions or commands.
Closes the session and returns to passive monitoring.
3.2 Key Differentiators
Feature
Driftwise Approach
Typical Solutions
Fact Quality
Edge-case, surprising, non-repetitive
Generic descriptions
Interaction
Voice-first, hands-free, interruptible
Screen-based, requires attention
Proactivity
Ambient delivery without prompting
Requires user to initiate
Adaptability
Adjustable cadence via voice
Fixed schedules or manual triggering
Context
Season and weather-aware
Static content

User Stories
4.1 Primary Persona: The Curious Commuter
A driver who regularly travels familiar routes and wants to discover something new about the places they pass through, without any active effort or distraction from driving.
US-1: Passive Discovery
As a driver, I want interesting local history facts delivered to me automatically so that I can learn about places without looking anything up.
Acceptance Criteria: Facts are delivered via voice without any user action required.

US-2: Audio Interruption
As a driver listening to podcasts, I want Driftwise to interrupt like a navigation prompt so that I don’t miss interesting facts while my audio continues afterwards.
Acceptance Criteria: Driftwise requests audio focus, delivers content, then releases focus.

US-3: Voice Control
As a driver, I want to adjust the frequency of facts using my voice so that I can get more facts in interesting areas or fewer on motorways.
Acceptance Criteria: Commands like “more often” or “less often” adjust the polling cadence.

US-4: Pause Capability
As a driver in conversation with a passenger, I want to say “pause” mid-delivery so that I can focus on the conversation and hear the rest later.
Acceptance Criteria: “Pause” interrupts delivery; “continue” resumes.

US-5: Follow-up Questions
As a driver who heard something intriguing, I want to ask a follow-up question in the 5 seconds after delivery so that I can learn more.
Acceptance Criteria: Follow-up questions are answered within the same Live API session.

US-6: Skip Fact
As a driver who isn’t interested in the current fact, I want to say “skip” so that Driftwise stops and waits for the next cycle.
Acceptance Criteria: “Skip” immediately ends the current session.

Functional Requirements
5.1 GPS & Location
ID
Requirement
Priority
FR-GPS-1
Poll device GPS at configurable intervals (default: 5 minutes)
Must
FR-GPS-2
Request high-accuracy location using Geolocation API
Must
FR-GPS-3
Fall back to network-based location if GPS unavailable
Should
FR-GPS-4
Skip cycle gracefully if no location can be determined
Must
FR-GPS-5
Support background location access (PWA with keepalive)
Must
5.2 Geocoding
ID
Requirement
Priority
FR-GEO-1
Reverse-geocode coordinates using Nominatim API
Must
FR-GEO-2
Extract multiple place names within 1-2km radius
Must
FR-GEO-3
Handle areas with sparse place data gracefully
Should
FR-GEO-4
Cache recent geocoding results to reduce API calls
Should
FR-GEO-5
Respect Nominatim usage policy (1 request/second)
Must

5.3 AI Integration
ID
Requirement
Priority
FR-AI-1
Use Gemini 3 Flash (or 2.5 Flash) with web search grounding for fact research
Must
FR-AI-2
Use Gemini Live API for voice delivery and interaction
Must
FR-AI-3
Enforce minimum 15-second gap between API calls (free tier)
Must
FR-AI-4
Include season and weather context in prompts
Should
FR-AI-5
System prompt must prioritise edge-case, non-generic facts
Must
FR-AI-6
Handle rate limit errors gracefully (skip cycle)
Must
FR-AI-7
Support API key configuration via environment variable
Must

5.4 Voice Interaction
ID
Requirement
Priority
FR-VOX-1
Open Live API session for each fact delivery
Must
FR-VOX-2
Request Android audio focus before speaking
Must
FR-VOX-3
Release audio focus after session ends
Must
FR-VOX-4
Listen for 5 seconds after fact delivery for follow-ups
Must
FR-VOX-5
Recognise “pause” command mid-delivery
Must
FR-VOX-6
Recognise “continue” command to resume
Must
FR-VOX-7
Recognise “skip” command to end session
Must
FR-VOX-8
Recognise “more often” / “less often” for cadence
Should
FR-VOX-9
Close session automatically after 5s silence
Must
FR-VOX-10
Support barge-in (user can interrupt mid-speech)
Should

5.5 Content Filtering
ID
Requirement
Priority
FR-FLT-1
Exclude generic descriptions (“known for”, “famous for”)
Must
FR-FLT-2
Prioritise specific dates, numbers, unusual events
Must
FR-FLT-3
Prioritise named individuals and their stories
Should
FR-FLT-4
Exclude repetitive regional facts (e.g., “Lake District scenery”)
Must
FR-FLT-5
Prefer facts with concrete, verifiable details
Should

Non-Functional Requirements
6.1 Performance
ID
Requirement
Target
NFR-P-1
GPS acquisition time (warm start)
< 3 seconds
NFR-P-2
Geocoding API response time
< 2 seconds
NFR-P-3
Gemini fact generation time
< 10 seconds
NFR-P-4
Live API session establishment
< 2 seconds
NFR-P-5
Total time from GPS poll to speech start
< 20 seconds
6.2 Reliability
ID
Requirement
Target
NFR-R-1
Graceful degradation on network failure
Skip cycle, retry next
NFR-R-2
Graceful degradation on API rate limit
Skip cycle, log warning
NFR-R-3
App should not crash on any API error
100%
NFR-R-4
Background operation stability

4 hours continuous

6.3 Usability
ID
Requirement
Target
NFR-U-1
Zero-touch operation after initial setup
100%
NFR-U-2
Voice command recognition accuracy

90%
NFR-U-3
Speech clarity and naturalness
Native audio quality
NFR-U-4
Time to first fact after app launch
< 60 seconds

6.4 Security & Privacy
ID
Requirement
Notes
NFR-S-1
API key stored in .env, never in code
Standard practice
NFR-S-2
No persistent storage of location history
Privacy by design
NFR-S-3
HTTPS for all API communications
Mandatory
NFR-S-4
Ephemeral tokens for Live API (production)
Google recommendation

Technical Architecture
7.1 High-Level Architecture
Driftwise follows a client-side PWA architecture with direct API integration. For V1 (personal use), the PWA connects directly to Google APIs using a stored API key. For production scaling, a backend proxy would be introduced for secure token management.
Component Diagram
[PWA on Android Phone]
├── Location Service (Geolocation API)
├── Geocoding Client (Nominatim)
├── Gemini Client (Text API with Grounding)
├── Gemini Live Client (WebSocket, Native Audio)
├── Audio Focus Manager (Android Audio API)
└── State Manager (React/Svelte state)

7.2 Technology Stack
Layer
Technology
Rationale
Framework
SvelteKit or Ionic/Capacitor
PWA-native hybrid with good device API access
State
Svelte stores or React Context
Simple, reactive state management
GPS
Geolocation API + Capacitor Geolocation
Background location support
Geocoding
Nominatim (OpenStreetMap)
Free, no API key required
AI (Text)
Gemini 3 Flash / 2.5 Flash with Grounding
Web search for current facts
AI (Voice)
Gemini Live API (WebSocket)
Bidirectional native audio
Audio
Web Audio API + Android Audio Focus
Audio interruption like nav prompts
Build
Vite + PWA plugin
Fast builds, service worker generation

7.3 Data Flow
7.3.1 Fact Generation Cycle
Timer fires (every 5 minutes)
Request high-accuracy GPS position
Send coordinates to Nominatim reverse geocoder
Extract place names from response (village, town, locality, etc.)
Construct prompt with place names, season, weather context
Call Gemini (text) with web search grounding enabled
Receive structured response with selected fact
Open Gemini Live API session
Request Android audio focus (duck other audio)
Stream fact to Live API for speech generation
Play audio output through device speakers
Listen for 5 seconds for follow-up or command
Close Live API session
Release audio focus
Reset timer for next cycle

API & Integration Specifications
8.1 Nominatim Reverse Geocoding
Endpoint: https://nominatim.openstreetmap.org/reverse
Method: GET
Rate Limit: 1 request per second (must respect)
Request Parameters
Parameter
Value
Notes
lat
{latitude}
From GPS
lon
{longitude}
From GPS
format
json
JSON response
zoom
14
Locality level detail
addressdetails
1
Include address breakdown

Response Fields of Interest
address.village, address.town, address.city, address.locality, address.hamlet, address.suburb, display_name

8.2 Gemini Text API (Fact Generation)
Model: gemini-3-flash-preview (or gemini-2.5-flash)
Endpoint: https://generativelanguage.googleapis.com/v1beta/models/{model}:generateContent
Grounding: Google Search enabled
Rate Limit: Free tier: ~5 RPM, enforce 15s minimum gap

8.3 Gemini Live API (Voice Interaction)
Model: gemini-2.5-flash-native-audio-preview-12-2025
Protocol: WebSocket
Endpoint: wss://generativelanguage.googleapis.com/ws/google.ai.generativelanguage.v1beta.GenerativeService.BidiGenerateContent
Session Limit: 10 minutes (ample for ~1 minute interactions)
Audio Format: PCM 16-bit, 16kHz input, 24kHz output

Session Flow
Open WebSocket connection with API key
Send BidiGenerateContentSetup with model, config, system instruction
Wait for BidiGenerateContentSetupComplete
Send text content (the fact to speak)
Receive audio chunks, play immediately
Stream microphone input for follow-ups
Close connection when done

System Prompt Design
9.1 Fact Generation Prompt
The following prompt template is used for the text-based fact generation step:
SYSTEM PROMPT (Fact Generation)
You are a local history researcher finding fascinating, unusual facts.

PRIORITISE:

Specific dates, measurements, quantities
Named individuals and their stories
Unusual events, firsts, records
Industrial, engineering, or scientific history
Connections to notable historical events
EXCLUDE:

Generic descriptions ("known for", "famous for", "picturesque")
Regional clichés ("traditional stone cottages", "scenic views")
Vague historical references without specifics
Tourism-brochure language
Current context: {season}, {weather_conditions}
Location(s): {place_names}

Return ONE fascinating fact, 2-3 sentences max, ready to be spoken aloud.

9.2 Live API System Instruction
The Live API session uses a simpler system instruction focused on delivery:

You are Driftwise, a friendly local history companion for drivers. Speak naturally and conversationally. Keep responses brief—the driver needs to focus on the road. If asked a follow-up question, answer concisely. Understand commands: “pause” (stop speaking), “skip” (end session), “more often”/“less often” (acknowledge cadence change).

Voice Interaction Design
10.1 Interaction States
State
Description
Transitions
IDLE
Waiting for next GPS poll cycle
Timer fires → LOCATING
LOCATING
Acquiring GPS position
Success → GEOCODING, Fail → IDLE
GEOCODING
Reverse geocoding coordinates
Success → RESEARCHING, Fail → IDLE
RESEARCHING
Querying Gemini for facts
Success → SPEAKING, Fail → IDLE
SPEAKING
Live API delivering fact
Complete → LISTENING, Pause → PAUSED, Skip → IDLE
LISTENING
5-second window for follow-up
Speech → SPEAKING, Timeout → IDLE
PAUSED
Delivery paused by user
Continue → SPEAKING, Skip → IDLE
10.2 Voice Commands
Command
Variations
Action
Pause
"pause", "wait", "hold on"
Pause current speech, enter PAUSED state
Continue
"continue", "go on", "resume"
Resume paused speech
Skip
"skip", "next", "stop"
End session immediately, return to IDLE
More Often
"more often", "more facts", "tell me more"
Reduce polling interval (min 2 min)
Less Often
"less often", "fewer facts", "quiet mode"
Increase polling interval (max 15 min)

10.3 Audio Focus Behaviour
Driftwise should behave like a navigation app when interrupting audio:
Request AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK
Other audio (podcasts, music) should duck (reduce volume) during speech
Release focus immediately after session ends
Other audio should restore to normal volume

Error Handling & Edge Cases
11.1 Error Scenarios
Scenario
Handling
User Feedback
No GPS signal
Skip cycle, retry next interval
None (silent)
Geocoding fails
Skip cycle, retry next interval
None (silent)
Gemini rate limit
Skip cycle, increase backoff
None (silent)
Gemini API error
Skip cycle, log error
None (silent)
Live API connection fails
Fall back to text notification
Optional toast
No interesting facts found
Skip cycle, don't speak
None (silent)
Network offline
Skip all API calls until online
Optional status indicator
Low battery
Increase polling interval automatically
Optional notification
11.2 Edge Cases
Sparse Data Areas
In areas with few named places or limited historical data, the app should gracefully skip the cycle rather than delivering generic content. The system prompt instructs Gemini to return a specific marker if no suitable fact is found.

Rapid Location Changes
If the user is travelling at high speed (motorway), the 5-minute interval may cover significant distance. The app does not attempt to track route or queue multiple facts—it simply uses the position at poll time.

Stationary User
If GPS position hasn’t changed significantly between polls (< 100m), the app should skip the cycle to avoid repeating facts about the same location.

Future Considerations (Out of Scope for V1)
12.1 Potential V2 Features
Location memory: Remember places visited, avoid repetition on regular routes
Route awareness: If destination is set, tease upcoming interesting locations
Speed awareness: Adjust behaviour when stationary vs moving
Interest profiles: Let user specify interests (industrial, military, literary, etc.)
Offline caching: Pre-fetch facts along predicted route for poor signal areas
Multi-user: Backend service for shared rate limits and caching
iOS support: Expand beyond Android
12.2 Scalability Considerations
For production deployment beyond personal use:
Backend proxy for secure API key management and ephemeral token generation
Response caching layer to reduce API costs for common locations
User authentication for personalisation features
Analytics for understanding usage patterns and improving fact quality
Paid tier integration for higher API limits
13. Success Metrics
For V1 (personal use), success is measured qualitatively:

Metric
Target
Measurement
Fact Quality
Genuinely interesting, not generic
Subjective assessment
Reliability
No crashes during 4+ hour journeys
Usage observation
Timing
Facts delivered smoothly, not jarring
Subjective assessment
Voice Quality
Natural, easy to understand
Subjective assessment
Interaction
Commands recognised reliably
Usage observation
API Stability
Stays within free tier limits
API dashboard monitoring

Glossary
Term
Definition
PWA
Progressive Web App - web application with native-like capabilities
Nominatim
OpenStreetMap's free geocoding service
Gemini Live API
Google's real-time bidirectional audio/video API
Grounding
Connecting AI responses to real-time web search results
Audio Focus
Android system for managing audio playback priority
WebSocket
Protocol for persistent bidirectional communication
Geocoding
Converting addresses to coordinates (or reverse)
Barge-in
User interrupting AI speech mid-delivery
VAD
Voice Activity Detection - automatic speech detection
Ephemeral Token
Short-lived authentication token for secure client access
— End of Document —
