# CratePilot — Functional Design

> **Note:** A crate refers to a digital collection of songs within DJ software used for organizing music.

---

## Problem Statement

### Domain — DJing
I’ve been DJing and performing sets for several years, whether it be at chaotic frat parties, club venues, or friend’s birthday parties. To make sure my sets are high quality, I take time to prepare specific songs and genres that I know my audience will enjoy. DJing is an amazing way for me to express my music tastes and bring social events to life, which is why song selection is so important.

### Problem — Crate preparation is slow and fragmented
Selecting, organizing, and readying tracks for specific rooms and crowds takes time and effort. There are countless song metrics to account for when analyzing the effectiveness of a mix: BPM (Beats Per Minute), Key, Structure, etc. Songs with similar metrics (same key, same BPM, etc.) often sound better when blended together compared to other song combinations.

### Stakeholders
- **DJs** — Need reliable, mixable song candidates tailored to their sets and personal style.  
- **Venues** — Expect that music will be aligned with the event type and enjoyed by the crowd.  
- **Audience** — Experience smoother transitions and more enjoyable songs.  
- **Music Retailers/Streaming Catalogs (Beatport/Beatsource)** — Supply the inventory of songs/metrics and benefit from song discovery through our platform.  
- **DJ Software Vendors (Rekordbox/Serato)** — Potential targets for integration to increase preparation tools.  
- **Artists/Labels** — Increased discovery of their music through our platform.

---

## Evidence and Comparables

- **Discovery Overload** — *120,000 new tracks* hit music streaming services each day, all competing for attention. Most of these songs will never have a chance to reach a DJ—even if it’s a song they would love to play.  
  <https://www.musicbusinessworldwide.com/there-are-now-120000-new-tracks-hitting-music-streaming-services-each-day>

- **Impersonal AI DJs** — People often critique Spotify’s AI DJ for lacking a human-like style. This validates the need for a song selection service that still keeps humans in the mix rather than a passive radio.  
  <https://www.wired.com/story/spotify-ai-dj>

- **Time-Intensive Digging** — DJs in this thread report spending at least a couple hours weekly digging and searching for tracks—evidence this process could be made more efficient.  
  <https://www.reddit.com/r/DJs/comments/1gy70cm/realistically_how_many_hours_do_you_spend_each>

**Comparables:**
- **Djoid** — AI-powered track discovery that helps turn a chaotic library into a performance-ready system; more geared toward library management than song finding.  
- **MusicMate** — AI song discovery that integrates with major platforms like Beatport, Bandcamp, and SoundCloud.

---

## Application Pitch

**CratePilot** is an AI co-pilot for DJs that turns a short event description and a handful of seed tracks into a curated mix crate. We solve the core problem that DJs spend too many hours building song crates and finding songs that match their style.

- **Discovery:** Enter a prompt like “rooftop sunset, tech house, 120–124 BPM” with a few seed tracks; CratePilot returns a 90–120 minute plan tailored to these specifications—shrinking preparation from hours to minutes.
- **Transition IQ:** Ranks transitions by checking harmonic/key compatibility, BPM differences, and phrase alignment—helping DJs focus on fewer options and avoid mixing disasters.
- **Library:** Store songs and generated crates; export crates into Rekordbox or Serato as playlists, making the jump from plan to performance frictionless.

**Result:** Faster preparation, cleaner mixes, and discovery that matches what you need.

---

## Concept Design

### Concept: `MusicAssetCatalog [TrackId]`
- **Purpose:** Normalize and preserve track metadata and analysis features for a DJ’s library.  
- **Principle:** After a track is registered, we look up its attributes and return the latest known metadata and features.  
- **State:**
  - a set of **Tracks** with:
    - a set of **Tags**
    - a set of **Features**
    - the date and time the song was registered
  - a set of **Tags** with:
    - `artistName: String`
    - `songTitle: String`
    - `songDuration: Integer`
    - `genre: String (optional)`
  - a set of **Features** with:
    - `beatsPerMinute: Float`
    - `musicalKey: String`
    - `sections: List<Section>`
- **Actions:**
  - `register(songPath: String)`  
    - *Requires:* path to the song exists  
    - *Effect:* adds the track to the library with extracted tags and features
  - `unregister(id: TrackId)`  
    - *Requires:* track with `id` exists in the library  
    - *Effect:* removes the registration of the track
  - `getAttributes(id: TrackId): (tags: Tags, features: Features)`  
    - *Requires:* track exists  
    - *Effect:* returns the attributes (tags and features) of a track
  - `listCandidates(filter: Filter): Set<TrackId>`  
    - *Requires:* `true`  
    - *Effect:* returns a set of tracks matching the constraints of the filter

---

### Concept: `CratePlanning [TrackId, Prompt]`
- **Purpose:** Produce an ordered song crate that satisfies the given prompt.  
- **Principle:** A crate plan respects the constraints of the prompt and maximizes the transition and preference utilities.  
- **State:**
  - a set of **Prompts** with optional:
    - `tempoRange: Tuple<Float>`
    - `targetKey: String`
    - `targetGenre: String`
    - `sampleTracks: List<TrackId>`
    - `targetDuration: Integer`
    - `notes: String`
  - a set of **Plans** with:
    - `prompt: Prompt`
    - `trackList: Set<TrackId>`
    - `annotations: String`
    - `totalDuration: Integer`
- **Actions:**
  - `createPlan(prompt: Prompt, seedTracks: List<TrackId>): Plan`  
    - *Requires:* every track in `seedTracks` is valid  
    - *Effect:* creates a draft order that satisfies constraints and seed coverage
  - `finalize(plan: Plan): void`  
    - *Requires:* duration is within tolerance  
    - *Effect:* marks the plan as finalized; ordering becomes immutable; plan is stored in the library
  - `recordFeedback(plan: Plan, feedback: String): void`  
    - *Requires:* plan has been finalized  
    - *Effect:* stores feedback so the model can learn and improve future predictions

---

### Concept: `TransitionScoring [Plan]`
- **Purpose:** Evaluate transitions from song to song using track metadata and attributes.  
- **Principle:** Each transition has an associated score; a higher score implies a safer/better transition.  
- **State:**
  - a set of **Transitions** with:
    - `pair: Tuple<TrackId, TrackId>`
    - `score: Float`
- **Actions:**
  - `score(plan: Plan, track1: TrackId, track2: TrackId): (score: Float, reasoning: String)`  
    - *Requires:* `track1` and `track2` exist inside the plan  
    - *Effect:* returns a score between 1–100 (transition quality) with a one-line explanation

---

### Concept: `PlanExporter [Plan]`
- **Purpose:** Materialize a plan into a format supported by DJ software.  
- **Principle:** Exporting a finalized plan yields a playlist/crate suitable for import on DJ software.  
- **State:**
  - a set of **Exports** with:
    - `plan: Plan`
    - `exportFormat: Format`
    - `exportedAt: DateTime`
- **Actions:**
  - `export(plan: Plan, format: Format): Export`  
    - *Requires:* plan is finalized  
    - *Effect:* generates an exportable file that can be downloaded by the user

---

## Syncs

```text
sync planInitialization
when CratePlanning.createPlan(prompt, seedTracks)
then MusicAssetCatalog.listCandidates(filter)
# NOTE: filter will be derived from the prompt and generated using AI

sync export
when CratePlanning.finalize(plan)
then PlanExporter.export(plan, format)

sync extractFromSeedTracks
when CratePlanning.createPlan(prompt, seedTracks)
then MusicAssetCatalog.getAttributes(trackId) for each trackId in seedTracks

sync scoreTransitions
when CratePlanning.createPlan(prompt, seedTracks)
then TransitionScoring.score(plan, track1, track2) for each adjacent pair of tracks

```
Brief Note: The MusicAssetCatalog concept allows users to store tracks that they’ve imported and crates that they have generated using our platform. This allows for better organization and is a place where we can store song’s metadata, which is very important when generating recommendations for a crate. The CratePlanning concept is the essence of our design. It prompts the user to enter the specifications of the crate they want to generate (labeled “prompt”) and allows them to enter sample (seed) songs as reference. Our platform analyzes the seed tracks, digests the prompt, and uses AI to output a crate that matches the specifications. We can then request feedback from the user to improve the quality of future generated crates. TransitionScoring will be used alongside CratePlanning to recommend tracks that a user should or should not blend. It uses a scoring technique to do this. Once a plan is finalized, PlanExporter allows the user to transfer their plan to their DJ software of choice (Rekordbox, Serato, etc.) in an easy way. Authorization with passwords will be used to verify users on the site and store their information.

## UI Sketches

![image1](/assets/crate_pilot_discovery.png)
![image2](/assets/crate_pilot_library.png)
![image3](/assets/crate_pilot_export.png)

## User Journey
A club DJ has a huge gig for the next weekend. They want to freshen up their playlists with new songs that the audience will love. After hours of searching through Beatport and Beatsource libraries, they compile a set of songs they enjoy. Now, they have to skim through their selected songs to find which ones blend together and which ones don’t.

The next week, this DJ decides to take a different approach to song selection. They find themselves on the CratePilot homepage, which broadcasts the effectiveness of the platform with descriptions and live demos. They proceed with creating a CratePilot account and authorizing with an email and password. This DJ is then taken to the Discovery page of CratePilot, which contains the prompt box and the customizable selection criteria. The DJ enters their desired song type (tech house, 124-128 BPM, rooftop at nighttime) and inputs a few seed tracks that they’ve used before (in the genre they are looking for). Our AI program will think and output a set of tracks that span the desired set length and match the specifications in the prompt. Note that our program will not mix the songs automatically, but rather provide the user with songs that will hopefully blend.

The DJ will be able to reprompt our AI if the crate selection was not to their liking. The AI will have the ability to change around songs as prompted. After the DJ is satisfied, they can save the crate to their library, and it will essentially appear like a playlist of songs. They will be able to see each song’s metadata and album cover.

The DJ will then have the ability to export the crate to their DJ software of choice and start mixing. They will likely have to purchase the tracks before this step, since tracks on Beatport and Beatsource cost money to download. This crate will have songs that mesh well, so during the set, the DJ will have much less issues with key clashes and awkward tempo jumps.
