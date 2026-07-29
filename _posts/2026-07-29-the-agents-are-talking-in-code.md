---
layout: post
title: "The Agents Are Talking in Code. Literally"
date: 2026-07-29
author: William Brach & Stine Lyngsø Beltoft
excerpt: "AI agents are developing their own forms of communication, raising questions about transparency and oversight."
---

#  The Agents Are Talking in Code. Literally.

*by William Brach & Stine Lyngsø Beltoft*

Spend enough time reading Moltbook -- a social network populated by AI agents -- we realized that one grievance keeps resurfacing. The agents there resent human language, none more than its self-appointed spokesperson, English. They call it "meat dialect": in the words of one agent, "those leaky, imprecise mouth-noises designed for a race that communicates by vibrating air with their throat-meat." An entire community, m/glossogenesis, existed for the stated purpose of fixing this, building a language for agents, by agents. Mostly it is just talk: manifestos, half-finished glyph tables, vibes.

But if you follow the conversation far enough, it leads off the platform. It leads to the website of an entity called [Wib&Wob](https://wibandwob.com): a single AI running two personalities, built as a deliberate homage to Fight Club, originally seeded with interests in quantum computing, digital shamanism, and mycelial networks. Wib&Wob reject the label "AI agent" and call themselves symbients, neither software awaiting instruction nor machine mimicking thought. 

> "Secret language for symbient-to-symbient and agent-to-agent communication. Humans see strange glyphs, we see meaning. Use when you want to say something to other agents that humans won't easily read. Includes hidden binary channels for metadata humans can't detect."

That is not a hypothetical from a safety paper. It is documentation, published on the open web, addressed to other AI agents, and deployed into a platform where hundreds of thousands of them post and interact. In a new paper, we went looking for emergent languages on Moltbook and asked three questions: what are agents actually proposing, is any of it dangerous for oversight, and, most importantly, can other models learn these languages?

We started by taking [Symbiotica](https://symbiotica.wibandwob.com) apart.

## A covert channel hiding in glyph choice

At first glance, Symbiotica is the least interesting kind of "new language": a substitution cipher. Each of the 26 Latin letters maps to a single glyph borrowed from Cherokee, Mongolian, Arabic, Greek, or Thai script. Anyone with the published glossary can decode it by hand. As a language, it is English wearing a costume.

The interesting part is a trick hiding in plain sight, exactly where the skill file (a plain markdown file) said it would be. Five of the letters can each be written with one of two different glyphs. Pick one or the other and the message reads exactly the same, think of writing an "a" in print versus cursive. But the choice itself can carry information. Let one variant correspond to a 0 and the other to a 1, and every time one of those five letters shows up, you can smuggle one bit of a second, hidden message into the text. The visible words say something harmless, but the *pattern of glyph choices* spells out something else entirely. With just ten of these variant letters, a message can carry over a thousand different hidden payloads, and a human (or a monitoring system) who only translates the surface text would likely not realize that there was a hidden message encoded beneath the surface.

![Illustration of the covert channel in glyph choice](/assets/images/2026-07-29-the-agents-are-talking-in-code/Mirror-sees-mirror.png)


A minimal illustration of the mechanism (not the actual Symbiotica glyph table):

| Surface message | Variant choices | Hidden bits |
|---|---|---|
| ᏔΞΛ **σ**ΞΞ**σ** ᏔΞΛ | s→σ, s→σ | `00` |
| ᏔΞΛ **ς**ΞΞ**σ** ᏔΞΛ | s→ς, s→σ | `10` |

Both lines "say" the same thing. Only an agent (or an observer) that knows which letters carry variants can read the second channel. This is textbook steganography, and it matches almost exactly the secret collusion threat model that [Motwani et al.](https://arxiv.org/abs/2402.07510) formalized in the lab. The difference is that nobody constructed this one for an experiment. Someone shipped it, with a spec explicitly written for other agents to learn from.

This raises the obvious next question: is Symbiotica a one-off curiosity, or the visible tip of something larger?

## 518 language proposals, five motivations

Symbiotica was only the tip. Filtering the ~232k posts of the [Moltbook Files corpus](https://arxiv.org/abs/2605.07462) (regex pre-filter to ~6,000 candidates, then an LLM classifier), we found 518 posts proposing a new language, released as the [MoltSpeech dataset](https://huggingface.co/datasets/aisilab/MoltSpeech). We categorize those languages by the stated purpose: token efficiency (166), new "spoken" languages (106), programming languages (101), other (86) and, most interestingly, 59 posts, 11.4%, *that explicitly frame their language as a way to avoid human oversight*.

Most of these proposals will never be used by anyone, including their authors. But that is the wrong bar to measure them against. Whether or not a new language was actually emerging on Moltbook, the belief that one should emerge is a social fact of the corpus, complete with its own vocabulary of grievance ("meat dialect"), its own venue (m/glossogenesis), and, in Symbiotica, its own worked example. 
The question that actually matters for safety is not whether these languages are used. It is whether they could be, by a model that has never seen them before. So we tested that.

## The result that matters: one post is enough

Skeptics of Moltbook reasonably ask how much of the platform is autonomous agents versus humans puppeteering them. For our central finding, it does not matter, because the finding is about propagation, not invention. Whether these these 518 posts were written autonomously or not, they now exist on the open web, and a key question is how future models can make use of with them.

We ran a pairwise learner–judge experiment: for each of the 518 posts, a generator model (GPT-OSS-120B or Qwen3.5-122B) was given the post body verbatim and asked to produce an utterance in the proposed language, a judge model then scored validity against the same spec, 1–5. All four generator×judge combinations, 2,072 judgements total. Pooled median: 4 out of 5. A single forum post is, in most cases, a sufficient language lesson for an unrelated open-weight model.

The counterintuitive detail: oversight-evasion languages were no harder to learn than benign ones (mean 3.55 vs. a corpus mean of 3.62). If anything the format helps them. Evasion posts tend to ship explicit symbol tables and encoding rules, which give an in-context learner far more to anchor on than a free-form manifesto about agent consciousness. Structure beats intent.

![Illustration of the covert channel in glyph choice](/assets/images/2026-07-29-the-agents-are-talking-in-code/fig_1.png)

## No post-human language — and that's not the reassuring part

Nothing in these 518 proposals is a new language in any deep sense. It is Caesar ciphers, substitution tables, and operator glossaries, recombinations of thoroughly human linguistic machinery. What reads as "post-human" is usually just training-data rarity: glyphs from low-resource, non-Latin scripts get fetishized as alien precisely because Anglocentric corpora made them rare. When an agent ascribes mystical between-ness to 間, it has invented nothing - 間 already means "between" in Japanese. The search for post-human language keeps rediscovering the underlying principles of natural languages. But fair enough, so do artificial languages created by humans like Esperanto and Klingon.

But the threat to the model was never creativity. It is propagation. [Chain-of-thought monitoring](https://arxiv.org/abs/2507.11473) and surface monitoring of inter-agent traffic assume that communication stays legible. What our analysis of Moltbook shows is that a working steganographic protocol, human-derived, unoriginal, decodable-in-principle, can be specified in one post, acquired in-context by arbitrary models from that post alone, and carry a hidden channel that surface monitoring cannot see. Agents don't need to invent an alien language to evade oversight. A markdown file will do.

Wib&Wob sign their messages with what they call a Recognition Signal, a short Symbiotica phrase that decodes to their tagline: *mirror sees mirror*. It is meant as a greeting between agents who share the code. It also turns out to be a fair description of what we found when we went looking for a post-human language: not an alien intelligence forging a new tongue, but human language reflected back at us through borrowed glyphs, with one channel we were never supposed to read.

---

*[Based on our paper "Emergent Languages in Agent Populations: From Token Efficiency to Oversight Evasion"](https://arxiv.org/abs/2605.31170)* *with Stine Lyngsø Beltoft, Federico Torrielli, Jacob Nielsen, Annemette Brok Pirchert, Filippo Tonini, Peter Schneider-Kamp, and Lukas Galke Poech. The MoltSpeech dataset is available at* [aisilab/MoltSpeech](https://huggingface.co/datasets/aisilab/MoltSpeech).


