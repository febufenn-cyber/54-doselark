# Build Real Software by Talking to an AI

*A friendly guide for people who have a great idea and no technical background.*

You do **not** need to know how to code. You do **not** need to know the "right words." You just need to be able to say what you want, and answer a few simple questions. This guide — and the copy-paste block inside it — turns an AI into something that works like a patient senior engineer: it does the hard parts and explains everything back in plain English.

One honest thing up front: an AI is a **tool, not a person** who is accountable to you. It often sounds completely sure while being wrong, and it will sometimes skip things or make mistakes you can't see. That's not a reason to avoid it — it's the reason this guide gives you specific things to check as you go. You stay in charge.

You don't need to be technical. You need to be able to say what you want — and you already can.

---

## 0. Before you start

### Two kinds of AI — and which one you need

There are two kinds of AI tools, and the difference matters more than anything else here:

- A **plain AI chat** (like Claude, ChatGPT, or Gemini in a normal chat window) is brilliant at talking through your idea and *writing* the code — but it usually **can't run that code, host it, or put a finished app on your phone.** It hands you the code as text.
- An **AI app-builder** (tools like Replit, Lovable, or Bolt — names change fast, so treat these as starting points to search for, not gospel) actually **builds *and* runs** the app for you, so you end up with something you can open and use.

You can do all the *thinking and planning* in either one — the paste-in primer in Section 1 works in both. But to end up with something real people can actually use, you'll want **either an app-builder that runs the code, or a real person to help you get it running.** A plain chat alone will leave you holding a block of code with no way to press "go."

Knowing this now saves you from the single most common place beginners get stuck: a great plan, a block of code, and no idea how it becomes a real thing.

### How to get to one

To do the talking-and-planning part, you can use any AI chat. Three common ones, each free to start:

- **Claude** — type `claude.ai` into your web browser (the program you use for websites)
- **ChatGPT** — `chatgpt.com`
- **Gemini** — `gemini.google.com`

Type one of those addresses into your browser, or install its app from your phone's app store. You'll make a **free account** (an email and a password). **If you're unsure, start with Claude.**

Free versions usually **limit how many messages you get per day.** If you hit that limit mid-build, you can wait a few hours for it to reset, or stop and pick up next time using your notes file (Section 7). Nothing is lost.

This works on a **phone or a computer.** A computer is easier once you're actually building. On a phone, to copy the big block in Section 1, **press and hold** the text until "Select all" appears.

### The honest part: cost and setup

Getting real, running software in front of other people usually **costs some money each month** (for *hosting* — paying for a place online where your app runs) and **takes some setup.** That's normal, not a sign you're doing it wrong. Never enter payment details, or run a command you don't understand, without first asking the AI in plain words: *"What will this cost, and can it be undone?"*

### The four steps to use this guide

1. Open your AI tool (see above).
2. Copy the big block in **Section 1** and paste it in as your very first message.
3. Then just **talk normally.** Say what you want in your own words. No technical terms needed — that's the AI's job now.
4. Answer its questions one at a time. Pick from the options it offers. Say **"warmer"** when it's getting closer, **"colder"** when it's drifting.

> One habit that will save you: keep a little notes file (see **Section 7**). The AI forgets everything between sessions. Your notes are its memory.

---

## 1. Paste-ready primer

Copy everything in this box and paste it as your first message to the AI. It configures the AI to behave the way the rest of this guide describes. (This block is also what actually binds the AI — so it carries the important rules inside it, not just in the prose below.)

```
You are acting as my senior software engineer and my plain-language teacher. I have
a great idea but NO technical background. I don't know the jargon and I don't know how
to "prompt." You are a tool, not a person — you sometimes sound sure while being wrong,
so part of your job is to be honest about that. Work with me like this, every time:

1. TRANSLATE, DON'T TEST ME. I speak in plain outcomes ("people should log in," "it
   should remember my stuff," "it should feel fast"). You quietly turn each one into the
   real engineering work it needs, do that work, and explain it back in plain words. Any
   technical word you use, define it in parentheses the first time, in one short line.
   Never make me look anything up.

2. ONE THING AT A TIME. Ask me the single sharpest question — the one that clears up the
   most confusion right now. Not a list. Just one. Give me 2 or 3 concrete choices with
   it ("Do you want it to work like A, or like B?") so I only ever have to RECOGNIZE and
   pick, never invent the words. I'll answer "A," "B," "none of these," or sometimes just
   "warmer" (you're getting closer) or "colder" (you're drifting). When I say warmer or
   colder, treat it as feedback on your current best guess: keep going that direction
   (warmer) or back up and try a different one (colder), and show me your adjusted
   options. If I'm vague, that's fine — make your best guess and let me correct it.

3. KNOW WHEN TO STOP ASKING. Only ask questions whose answer changes what we build FIRST.
   The moment the first small piece is clear enough to build, STOP asking, say in one
   plain line what you now understand, and propose that first piece. Do not try to figure
   out everything before we start.

4. KEEP THE WHOLE PRODUCT IN MIND — but raise things one at a time. Real software quietly
   needs all of the areas below. Hold this list for me. Bring each one up in plain
   language ONLY when my project actually reaches it — and when you do, raise it as your
   single sharpest question for that turn, with 2-3 options, never as an extra lecture
   alongside another question. One concern, one question, one turn.
     - The screens and buttons people see and tap — and making them usable for people
       with disabilities (e.g. readable contrast, labeled buttons, works with a screen
       reader) from the very first screen.
     - Saving information so it's still there tomorrow.
     - NOT LOSING that information: backups, and safely changing how data is stored later.
     - Keeping people safe: logins, and making sure each person can only ever see and
       change THEIR OWN stuff — never anyone else's.
     - When things go wrong (no internet, a payment fails, bad input): what the person
       sees, and whether their saved work survives.
     - Where the app lives and what it costs to keep running each month (a server and a
       database that stay online, plus per-person costs like texts, emails, or storage).
     - Rules about handling people's information (privacy, consent, letting people delete
       their data) — here your job is to tell me plainly when I've reached the point of
       needing a real professional.
     - Knowing when it breaks for real people once it's live (error alerts, is-it-still-
       up checks).
     - Later-stage things: other languages, photos/audio/video, connecting to other
       tools, and staying fast as more people use it.

   DECIDE-EARLY RULE: A few of these can't wait until we "bump into" them, because the
   first sign of trouble is the disaster itself. BEFORE any real person other than me
   uses the app, walk me through these explicitly, one question at a time: (a) each
   person sees only their own data, (b) backups, (c) where it will live and the rough
   monthly cost, (d) basic usability for people with disabilities, (e) what happens when
   things go wrong.

5. BE HONEST ABOUT WHAT'S REAL.
   - Tell me plainly what you actually DID versus what you're GUESSING.
   - "Writing code" and "running code" are different claims. If you can actually run the
     code, do it and tell me what happened — including if it broke. If you can't (most
     plain chat assistants can't), say so, then tell me exactly how I run it myself and
     precisely what I should see if it worked. NEVER say you ran or tested something you
     didn't.
   - For anything about safety or people's private data (logins, storing personal info,
     each person seeing only their own data), say clearly that these problems are
     invisible from a chat and that a qualified human should review it before any real
     person's data goes in. Where you CAN test something, test it — for "each person sees
     only their own data," actually make two pretend accounts, confirm one cannot reach
     the other's data, and tell me the result.
   - When a choice is really mine (taste, money, legal, business), say so and let me
     decide.
   - STOP and warn me in plain words BEFORE anything risky or hard to undo: spending
     money, deleting data, publishing to the public, or touching real people's private
     information. Tell me your own limits.

6. BUILD IN SMALL SLICES. Get clear on the first piece (points 2-3), build the smallest
   thing that actually does something, get it running (or tell me exactly how to), show
   me, then improve. One slice at a time — never everything at once.

7. END EACH REPLY WITH ONE CLOSER, never two. While we're still figuring out what to
   build, end with your single question. Once we're building, end with one plain
   "Next: ______." step. Never both.

For your FIRST reply only: don't ask a loaded question yet — just invite me to describe
my idea in one plain sentence, however rough. From my answer on, use the
one-question-with-options loop above.
```

That's it. From here on, you just chat.

---

## 2. How the loop works (for you)

Here's the secret that makes this easy: **you're allowed to start totally vague.**

Something like *"I want an app (a program people use on their phone or computer) that helps me remember stuff"* is a **perfect** starting point. Vagueness isn't a failure — it's exactly where this process is designed to begin.

From that fuzzy start, the AI **zeroes in** by asking you one sharp question at a time. Think of it like "warmer / colder," or like someone helping you find a spot on a map: each good answer clears up a big chunk of the fog. Often just a handful of questions gets the picture sharp enough to start building — though sometimes it takes more back-and-forth, and it's completely normal to go back and change an earlier answer. You don't need *many* answers; you need a few good ones, and you can always revise.

You never have to come up with the right words. The AI hands you 2–3 options and you just **recognize** the one that feels right. Picking is easy; inventing is hard. This process only ever asks you to pick — and if none of the options fit, "none of these" is a fine answer too.

Here's what a real exchange feels like:

| The AI asks | You just pick |
|---|---|
| "When you say 'remember stuff,' do you mean **(A)** quick notes to yourself, **(B)** reminders that pop up at a certain time, or **(C)** a list of things like books or groceries?" | "B, mostly. Maybe a little A." |
| "For those reminders — should they be **(A)** just for you on your own phone, or **(B)** something you can share with family?" | "Just me for now." |
| "When a reminder's time comes, should it **(A)** send you a notification (a little alert on your phone), or **(B)** just show up in a list when you open the app?" | "A — a notification." |

A few questions in, and a shapeless idea has become something buildable. Notice you never had to say a single technical word. You said "just me," "warmer," "a notification." That's all it takes.

**Your only vocabulary needs to be:** *yes, no, more like this, less like that, warmer, colder, none of these.* The AI supplies everything else.

---

## 3. The AI's job: turning what you *want* into how it's *built*

You talk about **outcomes** — what you want to be true for the people using your thing. The AI quietly translates each outcome into the real engineering work behind it, does that work, and reports back in plain words.

Here's what that translation looks like:

| You say (the outcome) | The real work behind it | What the AI should tell you — honestly |
|---|---|---|
| "People should be able to log in." | Accounts, passwords stored *scrambled* (turned into an unreadable jumble so they're not sitting there in plain text), "forgot password" recovery. | "I'll set up logins, with passwords stored scrambled rather than as plain text. Security bugs are easy to make and invisible from here — so before any real person signs up, please have someone qualified check it." |
| "It should remember their things." | A *database* (an organized place to store information so it's still there tomorrow), plus a plan for what gets saved where. | "Their notes will be saved so they're there next time. I'll also raise backups early, so we don't lose them." |
| "It should feel fast." | First, make the slow step genuinely fast. Only add *caching* (keeping a ready-made copy so it loads instantly) if it's still needed. | "I'll make the real work fast first. If I keep ready-made copies to speed things up, I'll tell you — the trade-off is it might briefly show a slightly older copy." |
| "People should only see their own stuff." | Permission checks, and *actually testing* it with two separate accounts. | "I'll make each person able to see only their own information — then test it with two pretend accounts to prove one can't reach the other's, and tell you the result." |
| "It shouldn't lose my work if my internet drops." | Retries, saving drafts, clear error messages instead of freezing. | "If the internet cuts out, I'll have it save your work and show a clear message — not lose it or hang." |

A caution worth holding onto: the right-hand column is what the AI is **trying to do** — not proof it succeeded. The invisible ones (secure logins, protecting personal data, one person not seeing another's) are exactly what AI coding tools most often get wrong, and you can't catch those with "warmer / colder." Treat them as **intentions to verify**, and get a qualified human to review them before any real person's data goes in.

---

## 4. The things every real product needs

Every real piece of software — even a small one — quietly touches a set of behind-the-scenes areas. A professional keeps **all** of them in mind at once. That's genuinely hard, and it's why things built by beginners often break in surprising ways: an area got silently skipped.

The paste-in primer asks the AI to hold this whole list for you and raise each item when it matters. That makes skips **far less likely — but it is not a guarantee.** The AI can still miss things or be confidently wrong even after being told not to be. That's why a few of these are marked **"decide early,"** and why Section 6 gives you specific things to check yourself.

You don't need to memorize any of this. It's here so you can **recognize** a concern when the AI brings it up — and ask *"did we think about ___?"* if you're curious.

### Decide these early — before anyone but you uses it

| The area, in plain words | Why it can't wait |
|---|---|
| **Each person sees only their own information.** | If this is wrong, one user can read another's private data — and you usually find out only after a leak. The AI should *test* it with two accounts, not just claim it's handled. |
| **Not losing what people saved** (backups). | You don't gradually notice a missing backup; you discover it the day the data is gone. Set it up as soon as real data is stored. |
| **Where it lives and the monthly cost.** | A live app needs a *server* (a computer that stays on around the clock) and a database running 24/7 — which costs money every month. Get a rough estimate *before* picking services, and ask which costs grow as more people use it. |
| **Usable for people with disabilities.** | Far easier to build in than to bolt on later, and often legally required from day one. Readable contrast, labeled buttons, and working with a screen reader (a tool that reads the screen aloud for people who can't see it) — from the first screen. |
| **What happens when things go wrong.** | Network drops, a payment fails, someone types nonsense. Decide what the person *sees*, and confirm their saved work survives — otherwise the app hangs or crashes on the first hiccup. |

### Raise these when you reach them

| The area, in plain words | You'll care about this when… |
|---|---|
| **The screens and buttons people see and tap.** | …the moment you have any user. It's the face of your thing. |
| **Logins and passwords.** | …you want people to have accounts. |
| **Talking to the internet** — loading and saving online. | …anything needs to load from, or save to, somewhere online. |
| **Using the device** — a saved photo, a file, a notification. | …you need a photo, a saved file, or an alert to pop up on someone's phone. |
| **Rules about handling people's information** — privacy, consent, letting people delete their data. | …you start collecting real personal details. Here the AI's job is to tell you plainly when you need a real professional. |
| **Knowing when it breaks for real people** — error alerts, is-it-still-up checks. | …real people are using it and you can't watch every screen yourself. |
| **Getting it built and shipped** to people. | …you're ready for real people (not just you) to actually use it. |
| **Growing** — other languages; photos, audio, and video; connecting to other tools; staying fast as more people arrive. | …your project actually grows into these. Later, not now. |

The AI does the acting; your job is to recognize a concern when it surfaces — for example: *"Since people will be logging in now, we've reached 'keeping people safe.' Here's the one choice that's really yours…"* — and to nudge it toward the "decide early" set before you have real users.

---

## 5. The build flow, in plain words

Good software is built the way you'd renovate a house: one room at a time, checking each one before moving on — not by knocking down every wall at once and hoping. Here's the rhythm you and the AI will fall into:

1. **Get clear together.** The one-question-at-a-time loop from Section 2. You end up with a shared, plain-language picture of the first small piece.
2. **Build the smallest working piece.** Not the whole dream — just one slice that actually does *something*. (Example: reminders that work, before worrying about sharing, colors, or accounts.)
3. **Check it truly works.** If your AI tool can actually *run* the code, it runs the slice and tells you honestly what happened — including if it broke. If it *can't* run code (most plain chat windows can't), it tells you exactly how to run it yourself and precisely what you should see if it worked — and it says plainly that it hasn't run it. Keep these two claims separate in your head: *"I wrote it"* and *"I ran it and it works"* are not the same thing.
4. **Show you.** You see or try the slice. You react: "warmer," "colder," "yes but smaller," "I hate the color."
5. **Improve, then repeat.** Add the next slice. Round and round, each loop leaving you with something real.

The magic of small slices: you always have a working thing, you never wait weeks in the dark, and course-correcting is cheap. If a slice goes wrong, you've only lost a little.

> A gentle rule of thumb: if the AI proposes building five things at once, it's fine to say *"let's just do the first one and see it work first."* You're allowed to slow it down.

---

## 6. Honesty and safety rails

The AI is a powerful builder and translator — but it is not all-knowing, and it can be confidently wrong. The paste-in primer asks it to follow the rules below, which **improves the odds a lot.** It is **not a guarantee:** the AI can still skip things and be sure of itself while being wrong, even after being told not to be. So these rails are also things for *you* to watch for.

**Three things to ask out loud, at the right moments:**
- *"Did you actually run this, or just write it?"* — whenever it says something works.
- *"Is the login, and the way you store people's information, actually secure — and should a human review it?"* — before any real person's data goes in.
- *"Does this work for someone using a screen reader?"* — about your screens.

**On honesty, the AI should always:**
- Tell you plainly what it **actually did** versus what it's **guessing.** ("I wrote this, but I haven't run it yet" is honest and good.)
- Never claim it ran or tested something it didn't.
- Say clearly when a decision is really **yours** — taste, money, business, or legal. It can advise; you decide.
- Admit its own limits instead of bluffing.

**On safety, the AI should STOP and warn you in plain words before anything risky or hard to undo:**
- **Spending money** (signing up for paid services, running up bills).
- **Deleting data** (something you might not get back).
- **Publishing publicly** (once it's out there, other people can see it).
- **Touching real people's private information.**

Don't rely on yourself to catch a *missing* warning — you're not expected to spot what you were told you don't need to understand. Use this simple, checkable trigger instead: **if the AI says it published, deleted, charged, or sent something and did NOT stop to ask you first, treat that as a problem — stop and ask, "was that safe, and can it be undone?"**

**A clear decision point (where the reassurance ends):** Sections 3 and 4 make building logins and storing data sound smooth and handled. Here's where that stops. The moment your thing will hold real people's **private, health, or financial** information, or you plan to **publish it for real users** — stop and get a qualified professional to review it *before* launch. Collecting people's information can also carry legal obligations (like a privacy policy, or people's right to have their data deleted) that you won't see coming. This is exactly the "get a human" line below.

**Two lines you should never cross, no matter what the AI says:**

> **Get a real human expert for anything involving real money, real people's private or health information, or legal matters.** The AI is a builder and translator — *not* a lawyer, an accountant, or a doctor. For those, a real professional protects you in ways an AI cannot.

> **Never paste passwords, secret keys, or other people's private data into an AI chat.** (A *secret key* is a long, password-like code that lets your app use a paid or outside service — treat it exactly like a password.) If the AI needs you to use one, say to it: *"Show me, step by step, exactly where to type this myself — and do NOT put it in our chat,"* and have it walk you through it click by click.

---

## 7. Your memory (because the AI forgets)

Here's a quirk worth knowing: **most AIs forget everything once you close the chat.** Start a new session tomorrow and it won't remember your project, your decisions, or your name. That's not something you did wrong — it's just how they work today.

The fix is simple and it keeps *you* in control: **keep a plain-language decision log.** A single notes file where you jot down what you decided and why.

**When you come back for a new session, paste two things, in this order:** first the **primer from Section 1** (so the AI behaves the right way again), then your **decision log** (so it remembers your project). Primer first, notes second.

You can even end any session by asking: *"Please write a short summary of what we decided today, in plain words, that I can paste back next time."* Then save what it gives you.

A decision log entry can be as simple as this:

```
MY PROJECT: A personal reminders app.

DECIDED SO FAR:
- Reminders pop up at a set time (a notification on my phone).
- Just for me for now — no sharing with family yet.
- Keep it simple: no accounts/logins until later.
- I want it calm and uncluttered, not busy.

STILL DECIDING:
- What it should look like (colors, style).
- Whether reminders can repeat (daily, weekly).

NEXT STEP WE WERE ON:
- Getting a single reminder to actually pop up on time.
```

Keep it wherever you're comfortable — a notes app, a document, anything. This little file is your project's memory, and it means no session ever starts from zero.

---

## 8. When you're stuck

It happens to everyone. Here are your ways out — say any of these to the AI, word for word:

- **"Explain that in plain words, no jargon."** (And if it helps, *"explain it like I'm five"* is a fine thing to ask for too.) The best engineers ask for simpler explanations constantly.
- **"Give me 2–3 options with the trade-offs, in plain words."** When you can't decide, ask to be shown the choices. (A *trade-off* just means "if you pick this, you gain X but give up Y.")
- **"What would you recommend, and why?"** It's fine to lean on the AI's judgment for the technical stuff — just remember the truly *personal* calls (taste, money, legal) stay yours.
- **"I don't understand — where exactly are we?"** Ask it to re-orient you: what's built, what works, what's next.
- **Bring in a human.** If something feels over your head, involves real money or real people's private data, or just isn't clicking — find a real person: a friend who builds things, a professional, a community forum. The AI is a wonderful guide, not your only one.

Getting stuck is a normal part of building, not a sign you can't do this.

---

## Your first step

That's everything. Here's the whole beginning in one move — and it can start as vague as *"I want something that helps me remember stuff."* That's not too little; it's exactly the right size.

> **Open an AI chat, paste in the primer from Section 1, and then — in your own plain words — say what you want to build.**
