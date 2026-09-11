# Voice Agent

A general-purpose voice agent: one engine that runs **any** short voice conversation — a booking,
a reminder, a feedback survey, a lead screen. What changes between use cases is a **checklist**,
not code.

Everything runs on a single `GEMINI_API_KEY` — the same key does the thinking, the listening
(speech-to-text) and the speaking (text-to-speech).

## Notebooks

| Notebook | What it is |
|---|---|
| **`voice_agent_full.ipynb`** | **Start here.** The complete agent: all 15 steps, including barge-in and a background reviewer. |
| `voice_agent.ipynb` | The same agent without barge-in and the reviewer — a shorter read. |
| `voice_survey_agent.ipynb` | The original feedback-only prototype, console mock engines. |
| `voice_survey_agent_voice.ipynb` | The same prototype with real speech in and out. |

## How it works

```
BEFORE THE CALL      1 describe the goal      2 build the checklist
                     3 check the checklist    4 rehearse      5 pre-record safe lines

EVERY TURN           6 hear start/stop        7 audio -> text
                     8 AI picks the reply     9 code validates the answers
                    10 save with a source    11 safety check, then speak
                    12 stop talking when they interrupt

AFTER THE CALL      13 final record          14 act on it     15 score the call
```

The one idea underneath it: **the model decides what to say; ordinary code decides what is true.**
The model only ever *proposes* an answer — a validator accepts or rejects it, and logs the reason.
Every stored value records which turn it came from, so an invented answer is a query away rather
than a transcript review.

## One engine, many use cases

Step 2 turns a plain-English goal into a checklist of slots. Every slot is one of five types —
`number`, `choice`, `text`, `time`, `date` — so the validator has five branches and never grows,
however many use cases you add.

```python
GOAL = "Call customers who booked a table to confirm the booking."
# -> booking_name (text) · booking_date (date) · booking_time (time) · party_size (number 1..20)

GOAL = "Remind patients about tomorrow's appointment and find out if they are coming."
# -> attending (choice: yes / no / reschedule)
```

Nothing below the checklist knows what a booking is.

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env        # then paste your key into it
jupyter notebook
```

Get a key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey). A real key starts
with `AIza`; an `AQ.` token is ephemeral and will start returning 401s.

**Audio is optional.** With a working microphone you talk to the agent yourself. Without one —
including on Colab — a second voice plays the customer, every speech round trip is still real, and
the whole call is saved to `call.wav`.

**Free-tier limits.** About 15 requests a minute, and a rehearsal outruns that; the notebooks wait
out the 429 and carry on.
