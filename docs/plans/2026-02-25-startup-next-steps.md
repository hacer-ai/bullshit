# Startup Next Steps Panel — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** After generating a bullshit phrase, reveal an over-the-top "YOUR UNICORN IS BORN" panel with 5 action cards that copy a crafted prompt to the clipboard and open the user's preferred LLM (Claude / ChatGPT / Perplexity).

**Architecture:** Pure HTML/CSS/JS — single file modification to `index.html`. No build system. Add HTML structure for the next-steps panel, CSS animations/cards, and JS functions for clipboard + open. The panel is hidden on load, revealed after each generation.

**Tech Stack:** Vanilla HTML, CSS (custom properties already in place), JavaScript (Clipboard API, window.open)

---

### Task 1: Add HTML structure for the next-steps panel

**Files:**
- Modify: `index.html` — add after the closing `</section>` of `.generator-card` (around line 226), before `</main>`

**Step 1: Add the panel HTML**

Insert the following block after line 226 (the `</section>` closing the vocabulary grid), before `</main>`:

```html
        <section class="next-steps" id="next-steps" aria-live="polite">
            <div class="next-steps-header">
                <div class="unicorn-badge">UNICORN STATUS ACHIEVED</div>
                <h2 class="next-steps-title">Your Startup Is Founded.</h2>
                <p class="next-steps-subtitle">The world is not ready. Do it anyway.</p>
            </div>
            <div class="actions-grid">

                <!-- Investor Deck -->
                <div class="action-card">
                    <div class="action-icon">📊</div>
                    <div class="action-info">
                        <h3>Investor Deck</h3>
                        <p>Your 10-slide Series A is inevitable.</p>
                    </div>
                    <div class="llm-buttons">
                        <button class="llm-btn claude" onclick="copyAndOpen('deck')">
                            <span class="llm-logo claude-logo"></span>Claude
                        </button>
                        <button class="llm-btn chatgpt" onclick="copyAndOpen('deck', 'chatgpt')">
                            <span class="llm-logo chatgpt-logo"></span>ChatGPT
                        </button>
                        <button class="llm-btn perplexity" onclick="copyAndOpen('deck', 'perplexity')">
                            <span class="llm-logo perplexity-logo"></span>Perplexity
                        </button>
                    </div>
                </div>

                <!-- Elevator Pitch -->
                <div class="action-card">
                    <div class="action-icon">🎤</div>
                    <div class="action-info">
                        <h3>Elevator Pitch</h3>
                        <p>30 seconds. $10 million. Go.</p>
                    </div>
                    <div class="llm-buttons">
                        <button class="llm-btn claude" onclick="copyAndOpen('pitch')">
                            <span class="llm-logo claude-logo"></span>Claude
                        </button>
                        <button class="llm-btn chatgpt" onclick="copyAndOpen('pitch', 'chatgpt')">
                            <span class="llm-logo chatgpt-logo"></span>ChatGPT
                        </button>
                        <button class="llm-btn perplexity" onclick="copyAndOpen('pitch', 'perplexity')">
                            <span class="llm-logo perplexity-logo"></span>Perplexity
                        </button>
                    </div>
                </div>

                <!-- Podcast -->
                <div class="action-card">
                    <div class="action-icon">🎙️</div>
                    <div class="action-info">
                        <h3>Launch a Podcast</h3>
                        <p>The world needs to hear this. Loudly.</p>
                    </div>
                    <div class="llm-buttons">
                        <button class="llm-btn notebooklm" onclick="openLink('https://notebooklm.google.com/')">
                            <span class="llm-logo notebooklm-logo"></span>NotebookLM
                        </button>
                    </div>
                </div>

                <!-- Y Combinator -->
                <div class="action-card">
                    <div class="action-icon">🚀</div>
                    <div class="action-info">
                        <h3>Apply to Y Combinator</h3>
                        <p>You are literally their next Airbnb.</p>
                    </div>
                    <div class="llm-buttons">
                        <button class="llm-btn yc" onclick="openLink('https://www.ycombinator.com/apply')">
                            <span class="llm-logo yc-logo"></span>Apply Now
                        </button>
                    </div>
                </div>

                <!-- Go Viral -->
                <div class="action-card action-card--wide">
                    <div class="action-icon">📣</div>
                    <div class="action-info">
                        <h3>Set the Acqui-hire Path</h3>
                        <p>LinkedIn. Hacker News. Reddit. Your moment is now.</p>
                    </div>
                    <div class="llm-buttons">
                        <button class="llm-btn claude" onclick="copyAndOpen('viral')">
                            <span class="llm-logo claude-logo"></span>Claude
                        </button>
                        <button class="llm-btn chatgpt" onclick="copyAndOpen('viral', 'chatgpt')">
                            <span class="llm-logo chatgpt-logo"></span>ChatGPT
                        </button>
                        <button class="llm-btn perplexity" onclick="copyAndOpen('viral', 'perplexity')">
                            <span class="llm-logo perplexity-logo"></span>Perplexity
                        </button>
                    </div>
                </div>

            </div>
        </section>

        <!-- Toast notification -->
        <div class="toast" id="toast"></div>
```

**Step 2: Verify in browser**

Open `index.html`. The panel should NOT be visible yet (we haven't added CSS/JS). No layout breaks.

---

### Task 2: Add CSS for the next-steps panel

**Files:**
- Modify: `index.html` — add inside the `<style>` block, before `</style>`

**Step 1: Add CSS**

```css
        /* ── Next Steps Panel ── */
        .next-steps {
            opacity: 0;
            transform: translateY(24px);
            pointer-events: none;
            transition: opacity 0.4s ease, transform 0.4s ease;
        }

        .next-steps.visible {
            opacity: 1;
            transform: translateY(0);
            pointer-events: all;
        }

        .next-steps-header {
            text-align: center;
            margin-bottom: 2rem;
        }

        .unicorn-badge {
            display: inline-block;
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            color: #fff;
            font-size: 0.7rem;
            font-weight: 800;
            letter-spacing: 3px;
            text-transform: uppercase;
            padding: 0.3rem 1rem;
            border-radius: 2rem;
            margin-bottom: 1rem;
        }

        .next-steps-title {
            font-size: 1.8rem;
            font-weight: 800;
            color: #fff;
            margin-bottom: 0.4rem;
        }

        .next-steps-subtitle {
            color: var(--muted);
            font-size: 0.9rem;
            letter-spacing: 0.5px;
        }

        .actions-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 1.2rem;
        }

        @media (max-width: 768px) {
            .actions-grid {
                grid-template-columns: 1fr;
            }
        }

        .action-card {
            background: var(--surface);
            border: 1px solid #004455;
            border-radius: 1.2rem;
            padding: 1.5rem;
            display: flex;
            flex-direction: column;
            gap: 1rem;
            position: relative;
            overflow: hidden;
            transition: border-color 0.2s ease, transform 0.2s ease;
        }

        .action-card:hover {
            border-color: var(--primary);
            transform: translateY(-2px);
        }

        .action-card--wide {
            grid-column: 1 / -1;
        }

        .action-icon {
            font-size: 1.8rem;
            line-height: 1;
        }

        .action-info h3 {
            font-size: 1rem;
            font-weight: 700;
            color: #fff;
            margin-bottom: 0.2rem;
        }

        .action-info p {
            font-size: 0.8rem;
            color: var(--muted);
        }

        .llm-buttons {
            display: flex;
            gap: 0.5rem;
            flex-wrap: wrap;
        }

        .llm-btn {
            display: flex;
            align-items: center;
            gap: 0.35rem;
            padding: 0.45rem 0.9rem;
            font-size: 0.75rem;
            font-weight: 600;
            border-radius: 2rem;
            border: 1px solid transparent;
            cursor: pointer;
            transition: all 0.15s ease;
            text-transform: none;
            letter-spacing: 0;
        }

        .llm-btn:hover {
            transform: translateY(-1px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }

        .llm-btn:active { transform: translateY(0); }

        .llm-btn.claude    { background: #cc785c; color: #fff; border-color: #cc785c; }
        .llm-btn.claude:hover { background: #d9896d; }

        .llm-btn.chatgpt   { background: #10a37f; color: #fff; border-color: #10a37f; }
        .llm-btn.chatgpt:hover { background: #1db88f; }

        .llm-btn.perplexity { background: #20b2aa; color: #fff; border-color: #20b2aa; }
        .llm-btn.perplexity:hover { background: #25c4bb; }

        .llm-btn.notebooklm { background: #4285f4; color: #fff; border-color: #4285f4; }
        .llm-btn.notebooklm:hover { background: #5a95f5; }

        .llm-btn.yc { background: #f26522; color: #fff; border-color: #f26522; }
        .llm-btn.yc:hover { background: #f47534; }

        /* LLM logo dots */
        .llm-logo {
            width: 14px;
            height: 14px;
            border-radius: 50%;
            flex-shrink: 0;
            background: rgba(255,255,255,0.3);
        }

        /* Toast */
        .toast {
            position: fixed;
            bottom: 2rem;
            left: 50%;
            transform: translateX(-50%) translateY(20px);
            background: #fff;
            color: #111;
            padding: 0.75rem 1.5rem;
            border-radius: 2rem;
            font-size: 0.85rem;
            font-weight: 600;
            opacity: 0;
            transition: opacity 0.2s ease, transform 0.2s ease;
            pointer-events: none;
            white-space: nowrap;
            z-index: 100;
            box-shadow: 0 8px 24px rgba(0,0,0,0.3);
        }

        .toast.show {
            opacity: 1;
            transform: translateX(-50%) translateY(0);
        }
```

**Step 2: Verify in browser**

Open `index.html`. Page should look identical to before (panel still hidden). No layout shifts.

---

### Task 3: Add JavaScript — prompts, clipboard, toast, reveal

**Files:**
- Modify: `index.html` — add inside `<script>`, after the `generate()` function

**Step 1: Add the JS block**

Add this entire block inside the `<script>` tag, after the closing `}` of `generate()`:

```js
        const LLM_URLS = {
            claude:     'https://claude.ai/new',
            chatgpt:    'https://chatgpt.com/',
            perplexity: 'https://www.perplexity.ai/',
        };

        function buildPrompt(type, phrase) {
            const p = {
                deck: `You are a world-class startup pitch deck consultant. Create a compelling 10-slide investor deck for a company called "${phrase}".

Slides: 1) Cover — name, tagline, logo concept. 2) The Problem — frame it as a civilizational crisis. 3) The Solution — how "${phrase}" solves everything. 4) Why Now — the token economy, the AI wave, the moment. 5) Market Size — assume $50B TAM, justify it confidently. 6) Product — describe the product in glorious buzzword-laden detail. 7) Business Model — how does it print money. 8) Traction — invent impressive metrics. 9) Team — describe the founding team as legendary. 10) The Ask — Series A, $20M, world domination.

Be bold. Be visionary. Be slightly unhinged. This is the future.`,

                pitch: `Write a 30-second elevator pitch for "${phrase}".

You are speaking directly to a partner at Sequoia Capital. They have 30 seconds before their next meeting. You have one shot.

The pitch must: reference the collapse of the old paradigm, position "${phrase}" as the inevitable next layer of civilization, include at least one statistic you made up with complete confidence, and end with a mic-drop closing line that makes them reach for their checkbook.

Format it as something they can read aloud. No bullet points.`,

                viral: `Write three viral posts to launch "${phrase}" into the spotlight:

1. LINKEDIN POST — Written by the "visionary founder". Tone: humble-brag meets prophecy. Start with a bold one-liner. Include 3 numbered "insights". End with a call to action. Use strategic line breaks for drama. Include 5 relevant emojis.

2. HACKER NEWS — "Show HN: ${phrase} – [write a compelling one-line description]". Then write a 3-paragraph comment as the founder explaining what it is, why it matters, and what you're looking for (beta users, co-founders, funding). Understated but compelling.

3. REDDIT POST for r/startups — Title and body. Tone: excited but grounded. Mention you "quit your job" to build this. Ask for honest feedback (you don't actually want it).

Make them all feel authentic, slightly embarrassing, and deeply shareable.`,
            };
            return p[type] || '';
        }

        function copyAndOpen(type, llm = 'claude') {
            const phrase = document.getElementById('result').textContent.trim();
            const prompt = buildPrompt(type, phrase);
            const url = LLM_URLS[llm];

            navigator.clipboard.writeText(prompt).then(() => {
                showToast('Prompt copied! Just paste it. They\'ll understand. 🚀');
            }).catch(() => {
                showToast('Open the link and paste your idea manually.');
            });

            window.open(url, '_blank');
        }

        function openLink(url) {
            window.open(url, '_blank');
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            toast.textContent = msg;
            toast.classList.add('show');
            clearTimeout(toast._timer);
            toast._timer = setTimeout(() => toast.classList.remove('show'), 3500);
        }

        function showNextSteps() {
            const panel = document.getElementById('next-steps');
            panel.classList.remove('visible');
            // Force reflow to restart animation
            void panel.offsetWidth;
            panel.classList.add('visible');
        }
```

**Step 2: Update `generate()` to call `showNextSteps()`**

At the end of the `generate()` function, after the `document.getElementById('stats').textContent = ...` line, add:

```js
            showNextSteps();
```

**Step 3: Verify in browser**

1. Open `index.html`
2. Click "Generate Bullshit"
3. The next-steps panel should fade+slide in
4. Click "Claude" on the Investor Deck card — clipboard should be written, claude.ai opens in new tab, toast appears
5. Click "Apply Now" on the YC card — YC apply page opens
6. Click the button again — panel re-animates with fresh phrase in prompts

---

### Task 4: Commit

```bash
cd /home/camilo/projects/experiments/bullshit
git add index.html docs/plans/2026-02-25-startup-next-steps.md
git commit -m "feat: add post-generation startup next-steps panel with LLM prompt actions"
```

---

## Notes

- `copyAndOpen` defaults to `claude` if no second arg passed — all Claude buttons use `onclick="copyAndOpen('deck')"` (no second arg needed since default is claude — **double-check the HTML uses the correct three-arg pattern as written in Task 1**)
- The panel re-animates on every new generation via the reflow trick (`void panel.offsetWidth`)
- Toast reuses the same element; `clearTimeout` prevents overlapping timers
- NotebookLM and YC have no prefilled prompt support — just `openLink()`
- All prompts embed the live phrase from `#result` at click time, not at generation time
