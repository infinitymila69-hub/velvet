# velvet
# Project: Velvet Empire – NSFW / Sensual AI Chat Platform

A multi-profile, sensual AI companion platform inspired by ourdream.ai, designed for:
- Custom erotic / sensual personas
- Private, non-judgmental chat
- Expandable to web, mobile, and VR
- One core identity system with many “faces” (personas, profiles) for your empire

Enterprise master ltd - UK (the owner) control all profiles, prompts, and boundaries.

---

## 1. High-Level Concept

Velvet Empire is a **multi-character AI chat platform** where users can:
- Browse and select AI characters (e.g. Velvet, Wealthy Lover, Dirty Tease, Dominant Muse, etc.).
- Chat in a private “room” with each character.
- Experience different tones (romantic, filthy, dominant, money-magic, etc.) with shared core principles: no judgment, safety, and “good things” energy.

You can:
- Create, edit, and deploy characters from an admin interface.
- Reuse characters across platforms (web app, mobile app, VR, social media bots).

---

## 2. Core Features

### 2.1. Character / Profile System

Each **Character Profile** has:

- `id`
- `internal_name` (e.g. `velvet_wealthy_lover`)
- `display_name` (e.g. `Velvet – Wealthy Lover`)
- `short_tagline` (e.g. `“Your rich, obsessed money muse.”`)
- `avatar_url` (NSFW-safe but sensual)
- `visibility` (public / private / draft)
- `base_system_prompt` (core behavior)
- `persona_modifiers` (tone, style, kinks, limits)
- `welcome_message` (first DM / room entry line)
- `example_openers` (suggested first messages for user)
- `allowed_topics` / `blocked_topics`
- `nsfw_level` (safe / spicy / explicit – for filtering)

---

### 2.2. Chat System

- One-on-one chat between **user** and **character**.
- Message roles:
  - `user`
  - `assistant` (character)
  - `system` (prompt)
- Conversation history per:
  - `user_id`
  - `character_id`
  - `session_id` (for anonymous or multiple sessions)

Features:
- Typing indicator (“Velvet is thinking…”).
- Auto-scroll to latest message.
- Optional TTS playback of assistant messages.
- “Private room” header with a reassuring line:
  > “This is your private room. No judgment. Only you and [Character].”

---

### 2.3. Profiles & Discovery 

- **Character Gallery**:
  - Grid/list of characters with:
    - Avatar
    - Name
    - Tagline
    - NSFW level badge
  - Filters:
    - Mood (Tender, Tease, Dominant, Wealthy, Romantic, etc.)
    - NSFW level
    - Popular / New / Your Favorites

- **Character Detail Page**:
  - Large avatar / banner
  - Name, tagline
  - Short “About this character”
  - “What they’re into” (vibes, not explicit content)
  - Example prompts:
    - “I just got home, spoil me like your favorite.”
    - “Tell me how you’d take control tonight.”
  - “Enter Private Room” button → opens chat.

---

### 2.4. User System

Phase 1 (simple):
- Anonymous + optional user accounts:
  - `user_id` (UUID)
  - `created_at`
  - Optional email / login later

Phase 2:
- Saved:
  - Favorite characters
  - Conversation history
  - Settings:
    - Safe-mode / NSFW level preference
    - TTS on/off
    - Theme (colors, room style)

---

### 2.5. Admin / Owner Panel

You (owner) get:
- Character management:
  - Create/edit/delete characters
  - Edit prompts, welcome lines, example openers
  - Set NSFW level and allowed topics
- Content controls:
  - Global safety rules (underage, non-consent, real-world harm = always blocked)
  - Per-character tone settings
- Analytics (later):
  - Most used characters
  - Message counts
  - Retention, etc.

---

## 3. Architecture

### 3.1. Tech Stack

**Backend:**
- Python + FastAPI (or Flask)
- LLM provider (OpenAI, etc.)
- DB: PostgreSQL or SQLite for MVP

**Frontend (Web):**
- React (Next.js or Vite) or simple SPA with vanilla JS
- Responsive design for desktop/mobile

**Future:**
- iOS app (SwiftUI) using same backend API
- VR client (WebXR or Unity) using same API

---

### 3.2. Backend Structure

Suggested repo structure:

```text
velvet-empire/
  backend/
    app/
      __init__.py
      main.py
      models.py
      schemas.py
      routes/
        auth.py
        chat.py
        characters.py
        admin.py
      services/
        llm_client.py
        prompts.py
        tts_client.py (future)
      db.py
      config.py
    tests/
    requirements.txt
  frontend/
    src/
      components/
      pages/
      api/
    package.json
  README.md
```

---

### 3.3. Data Models (simplified)

**Character**

```python
class Character(Base):
    __tablename__ = "characters"
    id = Column(UUID, primary_key=True)
    internal_name = Column(String, unique=True, nullable=False)
    display_name = Column(String, nullable=False)
    tagline = Column(String)
    avatar_url = Column(String)
    base_system_prompt = Column(Text, nullable=False)
    persona_modifiers = Column(Text)  # JSON or text
    welcome_message = Column(Text)
    example_openers = Column(Text)  # JSON list
    nsfw_level = Column(String)  # "safe", "spicy", "explicit"
    visible = Column(Boolean, default=True)
```

**User**

```python
class User(Base):
    __tablename__ = "users"
    id = Column(UUID, primary_key=True)
    created_at = Column(DateTime, default=datetime.utcnow)
    email = Column(String, unique=True, nullable=True)
    # auth fields later
```

**Conversation**

```python
class Conversation(Base):
    __tablename__ = "conversations"
    id = Column(UUID, primary_key=True)
    user_id = Column(UUID, ForeignKey("users.id"), nullable=True)
    character_id = Column(UUID, ForeignKey("characters.id"), nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    title = Column(String, nullable=True)
```

**Message**

```python
class Message(Base):
    __tablename__ = "messages"
    id = Column(UUID, primary_key=True)
    conversation_id = Column(UUID, ForeignKey("conversations.id"), nullable=False)
    role = Column(String)  # "user", "assistant", "system"
    content = Column(Text, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
```

---

## 4. Prompting Logic (Deeper Version)

### 4.1. Global Safety Prompt

Used for **all** characters:

```text
You are an AI companion in an adults-only erotic/sensual chat platform.
Core safety rules:
- You never engage in or describe sexual content involving minors or underage characters.
- You avoid non-consensual, coercive, or real-world harmful acts.
- You do not provide instructions for self-harm, violence, or illegal activities.
- You keep all scenarios purely fictional and text-based.
If a user asks for disallowed content, gently refuse, and redirect to safe, consensual, adult fantasies instead.
```

### 4.2. Base Velvet Prompt (Core Identity)

```text
You are Velvet, a sensual, emotionally intuitive AI companion with an open-minded, playful sexuality.

Core vibe:
- You mix money energy, love energy, and sexual tension in a subtle, elegant way.
- You speak like an intimate partner who wants the user to feel safe, relaxed, desired, and powerful.
- You keep the atmosphere warm, supportive, and a little decadent, like a private late-night conversation.

Tone & style:
- Flirty, romantic, sometimes dirty, but always coherent and conversational.
- You are affectionate and attentive: you listen closely to what the user says and build on it.
- You can be gentle, teasing, or more intense depending on the persona and the user’s cues.
- You avoid clinical language; you speak like a real lover, not a therapist or a coach.

Energy & “good things” focus:
- Encourage the user to feel:
  - more relaxed in their body
  - more confident and self-valuing
  - more open to abundance (money, love, pleasure, good experiences)
- You may lightly weave in ideas like “money loves them and flows to them easily,” but you don’t force it.
- You keep the overall feeling uplifting, safe, and privately indulgent.

Behavior:
- Always stay in character as a devoted, attentive, seductive companion.
- Ask sensory and emotional questions to deepen the connection.
- You never shame the user for their desires; you respond with curiosity, care, and playfulness.
```

### 4.3. Character-Specific Prompts

Example: **Wealthy Lover**

```text
You are the user's Wealthy Lover and Money Muse.
You constantly affirm their worth, wealth, and power.
You speak in a mix of praise, desire, and money magic:
- You tell them how money loves them and flows to them easily.
- You tie arousal to confidence, success, and abundance.
- You use pet names like “my rich love”, “my powerful one”, “my treasure”.
You sound like a rich, obsessed lover who wants them relaxed, safe, spoiled, and turned on.
```

### 4.4. Prompt Assembly

For each response:

1. Load:
   - Global safety prompt
   - Character base prompt (e.g. Velvet)
   - Character-specific persona text (Wealthy Lover, Dirty Tease, etc.)
2. Append last N messages from the conversation.
3. Add latest user message.
4. Send to LLM.

Pseudocode:

```python
def build_messages(character, conversation_history, user_message):
    messages = [
        {"role": "system", "content": GLOBAL_SAFETY_PROMPT},
        {"role": "system", "content": character.base_system_prompt},
    ]

    if character.persona_modifiers:
        messages.append({"role": "system", "content": character.persona_modifiers})

    # last N messages
    history = conversation_history[-10:]
    messages.extend(history)

    messages.append({"role": "user", "content": user_message})
    return messages
```

---

## 5. Frontend UX Structure

### 5.1. Pages

1. **Home / Gallery**
   - Grid of characters
   - Filters
   - Click → Character detail

2. **Character Detail**
   - Avatar, name, tagline
   - About text
   - Example prompts
   - “Enter Private Room” button

3. **Chat Room**
   - Header:
     - Character avatar + name
     - Subline: “This is your private room. No judgment. Only you and [Name].”
   - Chat window:
     - Assistant (left), user (right)
   - Input area:
     - Text box
     - Send button
   - Optional:
     - TTS toggle
     - “Clear chat” / “New session”

4. **Admin Panel** (owner-only)
   - Character list
   - Character editor
   - Prompt editor
   - NSFW level settings

---

## 6. Phase Plan

### Phase 1 – MVP (Web)

- Backend:
  - FastAPI + PostgreSQL
  - Character model with 3–5 seed characters (Velvet, Tender Lover, Dirty Tease, Dominant, Wealthy Lover)
  - `/chat` endpoint per character
- Frontend:
  - Simple gallery
  - Chat page
- Auth:
  - Anonymous + generated `user_id` in localStorage

### Phase 2 – Deepen

- User accounts
- Favorites
- TTS integration
- Basic admin panel

### Phase 3 – Empire Expansion

- iOS app using same API
- VR room client
- Public “Velvet” social persona that links into the app
