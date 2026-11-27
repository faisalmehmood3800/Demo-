---
name: ai-video-content-playbook
description: How the agency plans, scripts and prompts AI-generated video (Google Veo / Flow, Kling, Higgsfield, Nano Banana and similar) for reels, AI vlog characters, product hero clips, UGC-style ads and VFX shots, distilled from tutorials the user chose. Use it whenever the user wants an AI video or image prompt, a viral short-form script, a consistent AI character across clips, a product animation for a website hero, or advice on camera angles, lighting and lenses for generated footage, even if they only say "reel", "vlog", "Veo prompt" or "make a video with AI". Works with hyperframes and ultimate-video-editor, which assemble and edit the clips.
---

# AI video content playbook

Source: the AI content chapter of "AI full Course for Beginners" (youtube.com/watch?v=DaUxBGfYSVQ, 3:50 to 4:39, read in full) and the hero-asset parts of the website tutorials. Notes: `../pro-website-playbook/references/tutorial-notes-batch2-2026-11-27.md`, sections I and L. Credit counts, plan prices and view numbers there are the presenter's claims.

This environment cannot generate images or video itself. I write the script, the prompts and the shot list; the user (or the client) runs them in the generation tool and sends back the clips; then HyperFrames or `ultimate-video-editor` cuts, captions and exports. Say this plainly at the start so nobody waits for footage that will not appear.

## 1. Plan the story before any prompt

- One idea per video, written in a sentence ("Bigfoot tries shampoo for the first time").
- The hook is the first line and the first second: it has to stop the scroll ("Today I'm spending a whole day with a human"). Write three hooks and pick the strongest.
- Split the script into scenes of about 8 seconds (the usual clip length), 8 to 12 scenes for a 60 to 90 second video, each with one action and at most one or two lines of dialogue.
- Read the script aloud. Rewrite any line that is only filler; a generated video with a weak script still gets no views.

Why AI vlogs travel: they break the viewer's expectation, the character looks real, it speaks with a believable voice, and there is a small story. All four matter; the visuals alone are not enough.

## 2. Prompt formula (fill every part)

1. Camera style: selfie at arm's length, handheld with slight shake, vlog, static tripod, drone.
2. Character: species or person, size, colours, clothing or fur, one or two distinctive features. Keep this description word-for-word identical in every scene.
3. Environment: place, time of day, light (golden hour, overcast, neon street at night), weather, background details.
4. Action: one specific action (holding a fish, walking along a stream, washing hair). Vague verbs give vague clips.
5. Mood and expression: amused, tired, excited.
6. Audio and dialogue: voice quality (deep, gravelly, warm), the exact line, and who says it.

Template:

"[Camera style], [character description, identical every scene], in [environment, time, light]. [Character] [one specific action], [expression]. Audio: [ambient sound]. [Character] says in a [voice description] voice: '[exact line]'. Realistic, no text, no subtitles, no watermark."

Two characters at most in one clip, and label every line with the speaker ("the white kangaroo says: ...", "the brown Bigfoot replies: ..."). A third character or unlabelled dialogue makes the model mix voices and faces.

## 3. Keeping a character consistent

- Reuse the previous scene's prompt and change only the action and dialogue. Every changed word is a chance for the character to drift.
- When the tool supports it, save the best frame of a good clip as an asset and generate the next clip from it (Google Flow calls this frame-to-video; its Scene builder joins and trims clips).
- Generate several variations of every scene and keep the best; faces often break when the character turns, so cut before that frame.

## 4. Shot language that makes it look produced

Use these words in prompts on purpose; each one tells the viewer something.

| Choice | Use it for |
|---|---|
| Eye-level shot | Neutral, conversational scenes |
| Low angle | Makes the subject look powerful or dominant |
| High angle | Makes the subject look small or vulnerable |
| Over-the-shoulder | Conversations, a character looking at something |
| Tracking shot | Following a walking or moving subject |
| 360 orbit | Showing off a product or a hero moment |
| Push-in / slow zoom-in | Emotion, focus on a detail |
| Zoom-out / pull-back | Reveal the setting |
| Whip pan | Fast transition between scenes |
| Three-point lighting | Clean standard look, interviews, studio |
| High-key lighting | Bright fashion, beauty and product ads |
| Low-key lighting | Drama, thriller, horror |
| Natural light | Outdoor, documentary, authentic UGC |
| Motivated light | Light that seems to come from something in the scene (a laptop screen, a window) |
| 16 mm wide lens | Show the whole environment |
| 35 mm | Natural storytelling, street and vlog |
| 50 mm | Close to the human eye; talking heads |
| 85 mm | Flattering portraits, shallow background |
| Macro | Small details (product texture, food) |
| Telephoto | Distant subjects, compressed background |

## 5. Image first, then motion

For product clips, ads and VFX: write a natural still prompt ("shot on iPhone, natural light" for UGC; studio light for products), generate the still (Nano Banana, Gemini, ChatGPT images), pick the best of four, then animate it with image-to-video (Veo, Kling and similar), describing only the motion and keeping camera and background still unless the shot needs movement. Prompts for website hero assets, which must be text-free and match the page background, are in `pro-website-playbook` section 3.

## 6. Edit, then deliver

Assemble in HyperFrames or with `ultimate-video-editor`: trim slow starts, cut broken frames, speed up where the action drags, add captions, music with a licence you can show, and the client's logo at the end. Check the result on a phone before delivery.

## 7. Honest use

- Label realistic AI footage as AI where platforms require it (YouTube asks creators to disclose realistic synthetic content) and never present a generated person as a real customer, a real testimonial or a real event.
- No generated likeness of real people, brands or characters without permission.
- Use only paid plans, free tiers and trials as their terms allow. The tutorial showed getting a "student" plan with a VPN and a throw-away university email; that misrepresents who you are and breaks the provider's terms, so the agency never does it or recommends it.
- Check the commercial-use terms of each generation tool and music source before a client video goes out.

## 8. What this sells

Product hero clips for client websites, short ad and UGC-style videos for local businesses, reels for clients' social pages, and the agency's own content. Price it per finished video or as a monthly content package (see `agency-offer-playbook`); quote the generation-tool costs separately or include them knowingly.
