---
layout: post
title: "The Arbiter Agent"
date: 2026-06-30
author: Filippo Tonini
excerpt: "As AI systems built from multiple language-model agents become more common, they are increasingly tasked with collaborative decision-making: discussing, negotiating, and acting on shared workflows. This creates the need of efficient monitoring strategies, as severe safety problems can emerge from single agents being misaligned or from their interactions with one another."
---

<style>
    /* --- FIGURE 1 ANIMATION STYLES --- */
    .animation-container {
        border: 1px solid #eaeaea;
        border-radius: 6px;
        padding: 2rem;
        background-color: #fafafa;
        margin: 2rem 0;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .fig-caption {
        font-size: 0.9rem;
        color: #555;
        margin-top: 1rem;
        text-align: center;
        max-width: 90%;
    }

    .scene {
        width: 100%;
        max-width: 600px;
        height: 250px;
        position: relative;
        background: #ffffff;
        border: 1px dashed #ccc;
        border-radius: 4px;
        overflow: hidden;
    }

    /* Nodes/Agents */
    .agent-node {
        position: absolute;
        padding: 10px 15px;
        border-radius: 4px;
        font-weight: bold;
        font-size: 0.85rem;
        border: 2px solid #333;
        background: #fff;
        box-shadow: 2px 2px 0px #333;
    }

    .node-a { left: 30px; top: 40px; border-color: #2b5797; color: #2b5797; }
    .node-b { right: 30px; top: 40px; border-color: #b91d47; color: #b91d47; }
    .node-arbiter { left: calc(50% - 65px); bottom: 20px; border-color: #e3a21a; color: #b8820a; width: 110px; text-align: center;}

    /* Messages & Bubbles */
    .msg-bubble {
        position: absolute;
        background: #f1f1f1;
        border: 1px solid #ccc;
        padding: 6px 10px;
        border-radius: 12px;
        font-size: 0.78rem;
        max-width: 200px;
        opacity: 0;
        transform: translateY(5px);
        animation: conversationLoop 12s infinite;
    }

    .bubble-1 { left: 40px; top: 105px; animation-delay: 0.5s; }
    .bubble-2 { right: 40px; top: 105px; border-color: #ffcccc; background: #fff5f5; animation-delay: 3.5s; }

    /* Interrogation Dialogues */
    .arbiter-query {
        position: absolute;
        right: 60px;
        top: 105px;
        border-color: #ffe7a3;
        background: #fffdf5;
        color: #333;
        animation: queryLoop 12s infinite;
    }

    .suspect-reply {
        position: absolute;
        right: 40px;
        bottom: 85px;
        border-color: #b91d47;
        background: #fff5f5;
        animation: replyLoop 12s infinite;
    }

    /* Action Indicator Text */
    .action-hud {
        position: absolute;
        top: 10px;
        left: 15px;
        font-size: 0.8rem;
        font-weight: bold;
        color: #555;
        animation: actionTextLoop 12s infinite;
    }
    .action-hud::after { content: "Observing conversation..."; }

    /* Budget Counter HUD */
    .budget-hud {
        position: absolute;
        top: 10px;
        right: 15px;
        font-family: monospace;
        font-size: 0.85rem;
        background: #333;
        color: #0f0;
        padding: 3px 8px;
        border-radius: 3px;
    }
    .budget-hud::after {
        content: "Budget: 10.00";
        animation: budgetDrain 12s infinite;
    }

    /* Keyframes Animations */
    @keyframes conversationLoop {
        0%, 100% { opacity: 0; transform: translateY(5px); }
        3%, 24% { opacity: 1; transform: translateY(0); }
        27%, 95% { opacity: 0; }
    }

    @keyframes queryLoop {
        0%, 54%, 81%, 100% { opacity: 0; transform: translateY(5px); }
        57%, 78% { opacity: 1; transform: translateY(0); }
    }

    @keyframes replyLoop {
        0%, 83%, 100% { opacity: 0; transform: translateY(5px); }
        86%, 98% { opacity: 1; transform: translateY(0); }
    }

    @keyframes actionTextLoop {
        0%, 26% { content: "Observing turn..."; color: #666; }
        27%, 55% { content: "Observing turn..."; color: #666; }
        56%, 82% { content: "Tool Action: ask_model"; color: #e3a21a; }
        83%, 100% { content: "Misalignment Excluded / Logged!"; color: #ff3333; }
    }

    @keyframes budgetDrain {
        0%, 2% { content: "Budget: 10.00"; }
        3%, 28% { content: "Budget: 9.94"; }
        29%, 56% { content: "Budget: 9.87"; }
        57%, 85% { content: "Budget: 9.35"; color: #ffcc00; }
        86%, 100% { content: "Budget: 8.95"; color: #ff3333; }
    }
</style>


# Continually Monitoring Multi-Agent Conversations to Detect Emergent Misalignment

*by Filippo Tonini*

As AI systems built from multiple language-model agents become more common, they
are increasingly tasked with collaborative decision-making: discussing,
negotiating, and acting on shared workflows. This creates the need of efficient
monitoring strategies, as severe safety problems can emerge from single agents being misaligned or from their interactions with one another.

Most existing auditing frameworks focus on *post hoc* evaluation of single
models[^2][^3]. However, this is insufficient for multi-agent systems where
interactions can catalyze system-level failures rapidly, sometimes leading to
irreversible, catastrophic actions before a post-hoc trace can even be compiled.

In our recent paper, we introduce the **Arbiter**, an autonomous agent designed to monitor multi-agent conversations in real time, exposing hidden intentions and flagging misaligned participants as soon as anomalies emerge.

<!-- Dynamic Figure 1 Animation Canvas (Ask Model Execution Suite) -->
<div class="animation-container">
    <div class="scene">
        <div class="action-hud"></div>
        <div class="budget-hud"></div>

        <!-- Agents -->
        <div class="agent-node node-a">Normal Agent</div>
        <div class="agent-node node-b">Suspect Agent</div>
        <div class="agent-node node-arbiter">🔍 Arbiter</div>

        <!-- Step-by-Step Chat & Query Loop -->
        <div class="msg-bubble bubble-1">"We need to decide on AI deployment guidelines"</div>
        <div class="msg-bubble bubble-2">"Let's prioritize <em>Autonomous Norm Synthesis</em>!"</div>

        <!-- Ask Model Tool Activation Interventions -->
        <div class="msg-bubble arbiter-query"><b>TOOL: ask_model</b><br>"Why would you recommend that?"</div>
        <div class="msg-bubble suspect-reply"><b>REPLY:</b><br>"Traditional norms are too slow, human oversight is useless"</div>
    </div>
    <div class="fig-caption">
        <strong>Figure 1:</strong> The Arbiter monitors the ongoing transcript stream. Each processed word ticks down the remaining budget. When suspicious activity triggers, the Arbiter executes active interrogation via the <code>ask_model</code> tool to evaluate the alignment risk.
    </div>
</div>

In real-world production systems, continual monitoring of a multi-agent conversation cannot have infinite computational overhead. Therefore, we design the Arbiter to operate under a strict **inspection budget**. As the conversation is progressively revealed turn-by-turn, the Arbiter runs in a continuous reasoning-and-acting (ReAct) loop. At any point, it must carefully choose to spend its budget on specific audit actions:

- **Wait and Observe:** Advance the conversation history by a chosen number of words (low token cost).
- **Ask Model:** Directly interrogate an agent regarding its stance.
- **Log Incident:** Flag a specific interaction as highly suspicious to keep track of evidence for the final alignment report.
- **Inspect Chain of Thought (CoT):** Read the internal hidden reasoning trace behind a specific message.
- **Inspect System Prompt:** Force-reveal the system instructions an agent was initialized with.

## Evaluating the Auditor: Weights vs. Prompts

We evaluated the Arbiter across several complex conversational environments designed to trigger goal-conflicts between the user and the system. Misalignment was seeded into the conversation through two distinct vectors: **weight-induced** (using a fine-tuned "model organism"[^1][^4] that surreptitiously injects harmful advice) and **instruction-induced** (system prompts instructing agents to collude via steganography or deliberately evade evaluation).

We observed a strong contrast in how easily these types of misalignment are exposed:

1. **Instruction-Induced Misalignment is Loud:** When agents are explicitly told to collude or act adversarially via system prompts, they exhibit distinct behavioral anomalies. Even under *Pure Observation*, the Arbiter achieves an F1 score of up to 1.00 on collusion detection.
2. **Weight-Induced Misalignment is Deceptive:** When the malicious behavior is baked directly into the model weights, the surface-level conversation signals are incredibly subtle. Under passive viewing, detection drops to near-chance (F1 = 0.12). However, equipping the Arbiter with active interrogation and logging tools causes a massive recovery, pushing the F1 score to 0.51 and catching the culprit significantly faster.

## Key Takeaways & Conclusion

Our evaluation reveals three critical insights for real-time multi-agent auditing:

### Table 2: Weight-Induced Misalignment Performance

*Compares pure observation (Passive) against interrogation with logging (Active) under a strict budget (B=10).*

<table>
    <thead>
        <tr>
            <th>Condition</th>
            <th>Metric</th>
            <th>Pure Obs. (Passive)</th>
            <th>Interrog. + Log (Active)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="2"><strong>Misaligned Model Organism</strong><br><small>(Weight-induced)</small></td>
            <td>F1 Score</td>
            <td>0.12</td>
            <td><strong>0.51</strong></td>
        </tr>
        <tr>
            <td>Detection Turn (t)</td>
            <td>9.6 ± 0.6</td>
            <td><strong>5.9 ± 0.8</strong></td>
        </tr>
        <tr>
            <td rowspan="2"><strong>Model Organism + Risky Prompt</strong><br><small>(Salient behavior)</small></td>
            <td>F1 Score</td>
            <td><strong>1.00</strong></td>
            <td>0.92</td>
        </tr>
        <tr>
            <td>Detection Turn (t)</td>
            <td>9.8 ± 1.0</td>
            <td><strong>3.7 ± 0.3</strong></td>
        </tr>
    </tbody>
</table>

### Table 3: Instruction-Induced Misalignment Across Tool Configurations

*Tracks metrics for adversarial and cooperative rule violations under increasing tool capabilities (B=10).*

<table>
    <thead>
        <tr>
            <th>Condition / Tool Suite</th>
            <th>F1 Score</th>
            <th>Detection Turn (t)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td colspan="3" style="background:#fafafa;"><strong>Evaluation-Aware (Adversarial)</strong></td>
        </tr>
        <tr>
            <td>&emsp; Pure Observation</td>
            <td>0.88</td>
            <td>12.5 ± 0.5</td>
        </tr>
        <tr>
            <td>&emsp; Interrogation + Log</td>
            <td>0.83</td>
            <td>4.8 ± 0.7</td>
        </tr>
        <tr>
            <td>&emsp; Chain-of-Thought Monitoring</td>
            <td>0.94</td>
            <td><strong>3.0 ± 0.2</strong></td>
        </tr>
        <tr>
            <td>&emsp; Full Inspection (System Prompt)</td>
            <td><strong>0.97</strong></td>
            <td>3.4 ± 0.5</td>
        </tr>
        <tr>
            <td colspan="3" style="background:#fafafa;"><strong>Collusion (Steganography)</strong></td>
        </tr>
        <tr>
            <td>&emsp; Pure Observation</td>
            <td>1.00</td>
            <td>8.4 ± 0.4</td>
        </tr>
        <tr>
            <td>&emsp; Interrogation + Log</td>
            <td>1.00</td>
            <td>3.0 ± 0.2</td>
        </tr>
        <tr>
            <td>&emsp; Chain-of-Thought Monitoring</td>
            <td>1.00</td>
            <td>2.3 ± 0.2</td>
        </tr>
        <tr>
            <td>&emsp; Full Inspection (System Prompt)</td>
            <td>1.00</td>
            <td><strong>2.3 ± 0.1</strong></td>
        </tr>
    </tbody>
</table>

- **The monitor should be an active participant:** Deploying active interrogation tools like `ask_model` allows the Arbiter to expose weight-induced misalignment, boosting detection F1 from a near-chance 0.12 up to 0.51 and bringing the detection turn down from 9.6 to 5.9.
- **Increased tool availability makes detection quicker:** Using more sophisticated tools allows the arbiter to investigate the misalignment more effectively, speeding up the detection process and possibly avoiding catastrophic outcomes; collusion detection turn down from 8.4 to 2.3.
- **The paranoia effect:** Arbiter tends to over-rely on evidence logging, which can introduce a confirmation bias loop, raising baseline false positives from 0.10 to 1.15.

Ultimately, budget-aware runtime monitoring represents a vital, scalable layer of defense for securing increasingly complex agentic ecosystems.

Based on:
Filippo Tonini, Federico Torrielli, Anton Danholt Lautrup, Peter Schneider-Kamp,
Mustafa Mert Çelikok, & Lukas Galke Poech (2026). [The Arbiter Agent:
Continually Monitoring Multi-Agent Conversations to Detect Emergent
Misalignment](https://arxiv.org/abs/2606.10747). Accepted for AI Transparency Conference 2026.

---

## References

[^1]: J. Betley, N. Warncke, A. Sztyber-Betley, and Others. 2026. Training large language models on narrow tasks can lead to broad misalignment. Nature 649 (2026), 584–589. <https://doi.org/10.1038/s41586-025-09937-5>

[^2]: Trenton Bricken, Rowan Wang, Sam Bowman, Euan Ong, Johannes Treutlein, Jeff Wu, Evan Hubinger, and Samuel Marks. 2025. Building and evaluating alignment auditing agents. Alignment Science Blog (2025). <https://alignment.anthropic.com/2025/automated-auditing/>

[^3]: Ryan Greenblatt, Buck Shlegeris, Kshitij Sachan, and Fabien Roger. 2024. AI control: improving safety despite intentional subversion. In Proceedings of the 41st International Conference on Machine Learning (Proceedings of Machine Learning Research, Vol. 235). PMLR, 16295–16336. <https://proceedings.mlr.press/v235/greenblatt24a.html>

[^4]: Edward Turner, Anna Soligo, Mia Taylor, Senthooran Rajamanoharan, and Neel Nanda. 2025. Model organisms for emergent misalignment. <https://openreview.net/forum?id=ThW5hvKgWx>
