# Proverbs Live - Live Family Quiz Show Web Platform

A live TV-show-style quiz for playing with family at Christmas using Spanish proverbs. A host with full control over pacing, scoring, and validation runs the show from a dedicated console. A central TV acts as live scoreboard and bracket, and, depending on the minigame, also shows questions, answers, and real-time game state, while mobile phones serve as buzzers for options and turn-taking in some games and are not used at all in others.

## Motivation

The starting point is a ~100-page handwritten book of proverbs collected by the grandfather, now digitized. The idea is to bring it to life as a family game one Christmas evening, with around 15 people playing together in the living room.

**Private corpus:** The complete family deck remains **private** (strictly for family use).
The public repository will only use a **public-domain English corpus** for development, testing, and evaluation.

---

## 1. Show Format

The show is designed for a family evening (around 15 people) with a TV as the visual centerpiece and a host directing the event.

| Phase | What happens | Where it is shown |
|------|----------|-------------------|
| **Phase 1 - Individual qualifier** | Everyone plays a quick round on their phone to select the **4 captains** (highest scores). | Phones + ranking on TV |
| **Team creation** | The 4 captains take turns picking teams, schoolyard style. The host confirms the final composition. | TV + host console |
| **Team competition (includes semifinals + final)** | Teams compete in minigames and accumulate points. The 4 teams pair up into two semifinals, then a final; bracket shown on TV at key moments. | Scoreboard + bracket on TV |

> [!NOTE]
> The host decides the minigame order, the duration of each round, and when each vote or answer closes. Nothing moves forward without the host's signal.

---

## 2. Proposed Minigames (Open List)

Below are example games illustrating the style and difficulty; the final set may be these or others; the design space is open and part of the original contribution. Each one is tagged with its primary interaction mode: **real-time mobile (WebSocket)**, **on-stage voice**, or **host-judged**.

We prefer mobile for quick option-choice (Kahoot-style) and buzzer/turn-taking, avoiding typing where possible.

| # | Game | How it plays | Mode |
|---|------|--------------|------|
| **a)** | **Last-proverb-standing** | Two teams alternate saying valid proverbs from the corpus. A team is eliminated when a player fails, repeats a proverb, or runs out of time. Last team standing wins. | On-stage voice |
| **b)** | **Missing word** | A proverb is shown with one hidden word plus answer options (e.g. *"The cobbler's children have no ___" → shoes*). Fastest correct answers score most. | Real-time mobile (WebSocket) |
| **c)** | **Split halves** | First and second halves are shown on TV; players say the match by voice and the host marks whether it is correct. Variant: 10 + 10 shuffled halves on screen to pair up against the clock. | On-stage voice (host validates) |
| **d)** | **3x3 memory grid** | 9 proverbs are visible for 10 seconds, then hidden. Players answer by choosing between options or saying the answer by voice, avoiding typing where possible. | On-stage voice / mobile options |
| **e)** | **Explain the meaning** | One player explains the meaning of a proverb or invents a situation where it applies. The rest of the room or the presenter validates it. | Host-judged |
| **f)** | **Situation → proverb** | A situation is presented and players must answer with the fitting proverb. | Host-judged |

> [!WARNING]
> The host **is always in control**: they can pause, repeat a round, correct scores manually, and force the game forward. Every minigame includes an **override button** to resolve ties, technical failures, or family disputes without breaking the show's flow.

---

## 3. Architecture & Stack

The platform uses an authoritative server with lightweight clients, optimized for low-latency living-room play.

- **Backend - FastAPI + WebSockets (authoritative):** single source of truth for game state, timing, scoring, and validation; pushes real-time updates to all views.
- **Frontend - Vue 3 with views for TV, host and mobile:**
  - **Public TV view** - scoreboard, bracket, current turn, and minigame status.
  - **Host console** - pacing, scoring, overrides, and validation control.
  - **Mobile client** - option choice (Kahoot-style) and buzzer / turn request, avoiding typing where possible.
- **Network:** local network configured so all devices reach the game server (only accessible on LAN).
- **Speech + validation:** inclusion of **Whisper** will be considered for transcribing and progressively showing answers on screen; validation via deterministic methods and/or LLMs (open decision). See Living-Room Infrastructure for details.

---

## 4. Living-Room Infrastructure

The goal is a local network where every device reaches the game server (only accessible on LAN).

- **Local network**
  - Phones and TV connect to the local network to reach the game server.
  - Nice-to-have extra: specialized network management software/system (e.g. OpenBSD, OpenWrt) to limit internet access during the show (e.g. avoiding WhatsApp notifications popping up mid-show).
- **TV / scoreboard**
  - One large TV as the main screen: scoreboard, bracket, current turn, and status of each minigame.
  - Separate views for the host console (control) and the public view (show).
- **Audio + speech recognition**
  - Stage microphone + inclusion of **Whisper** will be considered for transcribing spoken answers and progressively showing them on screen.
  - Decision still **open**: local Whisper if a GPU is available, otherwise remote API or another PC on the LAN, depending on available hardware before the playtest.
- **Validation**
  - Validation via deterministic methods (e.g. normalization + edit distance) and/or LLMs, open decision. The host has the final word.

---

## 5. Current Status

- **Week 1** of the project.
- **Requirements v1 in progress:** show format, minigame families, and technical constraints. 
- **Next step:** confirm the final scope with the professor.

