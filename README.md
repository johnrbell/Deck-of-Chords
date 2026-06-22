# Music Tutor for Visually Impaired

## Foundational To-Do: 

### App Name: 

Flash Chords  
	Deck of Chords  
	Braille Scale  
Tact Tune

## Tooling:

* **Cursor Pro** \- $20/mo  
* **Claude API** \- Pay Per Usage

  *Claude API will report per usage request, as well as individual cost associated with them. Generally, full time web development was peaking at $2500-$3000, mo. I would recommend an account dedicated to this project, with Simina as the owner.* 


  *Initial Estimate for this spec is 50 hours, with a high maximum of 100 hours.* 

* **UI/Architecture**: SwiftUI, MVVM pattern.  
* **Accessibility**: Native iOS VoiceOver (UIAccessibility, UIAccessibilityTraits, AVSpeechSynthesizer for custom non-VoiceOver prompts).  
* **Vision Layer**: AVFoundation (AVCaptureMetadataOutput for high-speed QR/Barcode detection).  
* **Audio Layer**: AudioKit (or native AVAudioEngine with YIN/Auto-correlation pitch detection algorithms).

## Phase 1: Physical Card Sequencing

1. **Target Assignment**: App audibly announces the target sequence (e.g., "Arrange these notes: C, C, D, E, C, E, D. 7 cards total.").  
2. **Camera Acquisition (State Machine):**  
   * *Searching:* Camera active. VoiceOver gives positioning hints if partial cards seen (e.g., "I see 4 of 7 cards. Move camera higher.").  
   * *Acquired:* "7 cards detected. Hold still."  
   * *Evaluating:* Engine decodes QR strings and compares against target array.  
3. **Vision Feedback**: Uses user-defined Granularity Setting

## Phase 2: Audio Performance Verification

1. **Prompt**: "Cards correct. Now play the sequence on your piano."  
2. **Listening (State Machine):**  
   * *Tracking:* Pitch detection actively isolating fundamental frequencies, filtering out piano harmonics.  
   * *Timing Tutoring (If Active):* Metronome overlay engages. Notes must be played within an X-millisecond tolerance window.  
   * *Free Play (If Timing Inactive):* App waits indefinitely for the correct sequential pitches, ignoring rhythmic spacing.  
3. **Audio Feedback**: Evaluates played notes against target array based on Granularity Setting.

## Technical Details: 

### Visual Tutor:

Going with QR codes is a highly pragmatic choice. It shifts the technical challenge from complex optical character recognition—which can easily fail under varied lighting or slight physical rotations—to native metadata scanning, which iOS handles flawlessly right out of the box using `AVFoundation`.

Knowing the expected card count ahead of time is also a massive advantage for the logic flow. We can give the AI agent very strict parameters to build a robust state machine for the camera sequence. It will look something like this in the spec:

* **State 1: Searching** (e.g., "Looking for sequence of 7\. Currently see 4\. Move camera higher.")  
* **State 2: Acquired** (e.g., "7 cards detected. Hold still.")  
* **State 3: Evaluating** (e.g., "Checking sequence against target: C-C-D-E-C-E-D.")  
* **State 4: Reporting** (e.g., "Card 3 is incorrect. Expected D, found E.")

### Audio Tutor: 

To handle this cleanly on iOS, using standard auto-correlation or YIN algorithms (available natively or via frameworks like AudioKit) will be essential to isolate the fundamental frequency of each note without getting tripped up by those upper harmonics.

Reach: Adding a **Timing Tutoring** toggle with a metronome completely evolves this from a simple sequence checker into a true interactive rhythm coach. 

### Accessibility/Reporting Feedback:

Native VoiceOver is absolutely the way to go. Blind iOS users are already power users of Apple's built-in screen reader. By relying on native `UIAccessibility` protocols, the student can use the exact same swipe-and-double-tap gestures they use for every other app on their phone.

For the error granularity, we can map out a "Feedback Detail" toggle in the app's settings, giving the student control over how much coaching they receive.

### User Preferences:

* Timing Tutoring: Bool (Default: False). Engages metronome and rhythm validation.  
* Feedback Granularity: Enum (Simple | Detailed).  
  * *Simple Error (Cards):* "Incorrect order. Try again."  
  * *Detailed Error (Cards):* "Card 3 is incorrect. Expected D, found E."  
  * *Simple Error (Audio):* "Wrong note played."  
  * *Detailed Error (Audio):* "You played an E, but the card says D."

