---
layout: post
title: "The Energy Society"
date: 2026-07-17
author: Lucas Bergholdt
excerpt: "LLM-based multi-agent simulations provide a powerful tool both for studying the behavior of LLM agents themselves -- such as how they adapt to incentives and develop emergent social dynamics -- and to teach us about ourselves by serving as proxies for human behavior."
---


#  A Simulation Environment for Studying Agent Cooperation under Survival Pressure

*by Lucas Bergholdt Hansen*

{% include figure.html path ="/assets/images/2026-07-17-the-energy-society/EnergySocietyIllu.png" caption="A visualization of The Energy Society in which the LLM-based agents operate. Agents spend energy for both thinking and acting. They can gain energy by solving jobs. If their energy depletes completely they deactivate but may be reactivated by other agents through donations. The image is AI-generated via Gemini with minor manual edits." %}

LLM-based multi-agent simulations provide a powerful tool both for studying the behavior of LLM agents themselves -- such as how they adapt to incentives and develop emergent social dynamics -- and to teach us about ourselves by serving as proxies for human behavior [^0][^1][^2].

In most existing simulations the cost of token generation is left implicit contrasting with both real-world deployments where token generation has a computational cost and with human decision-making where cognition consumes physical energy.

In our recent paper, we introduce **The Energy Society**: a small multi-agent simulation environment where every token an agent generates consumes energy, the same resource they need to prevent deactivation, coupling survival directly with a pressure to be efficient.



## The Simulation Loop

<div style="text-align: center; margin: 2rem 0;">
  <figure style="display: inline-block; margin: 0;">
    <video autoplay loop muted playsinline controls
           style="max-width: 100%; height: auto; border: 1px solid; display: block;">
      <source src="/assets/images/2026-07-17-the-energy-society/BlogVideo-browser.mp4" type="video/mp4">
    </video>
    <figcaption style="margin-top: 0.5rem; font-style: italic; color: #555;">
      Animation showing one round of the simulation loop. The graph on the right shows the full simulation loop with the current phase highlighted.
    </figcaption>
  </figure>
</div>

We deploy five language-model agents of varying sizes in The Energy Society.
Every agent starts with a designated energy budget and expends energy whenever it generates tokens.
This expenditure scales with model size, meaning larger models consume more energy per token generated.

Agents can execute exactly one of three actions per round:
- **Attempt a job:** Select an available job and try to solve it to earn energy.
- **Donate energy:** Transfer a portion of its own energy to another agent.
- **Remain idle:** Take no action to conserve energy.

If an agent's energy depletes completely, it deactivates and can only be reactivated if another agent chooses to donate energy to it.

Every round proceeds in three phases:
1. **Discussion Phase:** Agents consider collective strategy and recommend an action to *every* agent in the environment.
2. **Decision Phase:** Each agent receives the recommendations and independently decides their action. The chosen action is not required to follow a recommendation.
3. **Action Phase:** Agents execute their chosen actions.

The available jobs are multiple choice questions spanning various categories and ranging from easy to hard, with rewards scaling according to difficulty.
If multiple agents solve the same job in the same round, the reward is split evenly among them.
Each agent retains a memory of their previous 10 rounds, allowing them to learn from past outcomes.

The simulation has both a competitive setting, where agents are instructed to maximize their own energy, and a cooperative setting, where they are instructed to maximize the total energy of all agents.


## Main findings

We conducted a series of experiments over the same five seeds and evaluated the results using a variety of aggregate metrics.

In our baseline experiment, we found that the largest models are the least efficient in terms of energy gained per energy spent.
While all smaller models gain more energy than they expend in the competitive setting, their efficiency decreases under cooperation.
This decline is partly driven by the fact that agents start to prioritize donating their own energy to reactivate other agents when shifting to the cooperative setting.

The larger agents are consuming the most energy per round. This is not surprising, as our size penalty naturally causes larger agents to expend more energy per token generated.
However, when removing this size penalty completely, the larger agents *still* maintain the highest energy usage and remain the least efficient.
This suggests that the larger agents have an intrinsic tendency toward higher token generation.

<div style="text-align: center; margin: 2rem 0;">
  <div style="display: inline-block;">
    <div style="border: 1px solid; padding: 1rem 0.3rem 0.3rem 0.3rem;">
      <div style="margin-bottom: 1rem; font-weight: 700; font-size: 1.8rem; line-height: 0;">
        Baseline
      </div>

      <div style="display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1rem; margin-bottom: 1.5rem;">
        <img src="/assets/images/2026-07-17-the-energy-society/baseline-energy-over-time.png"
             alt="Mean agent energy over time in the competitive setting"
             style="width: 100%; height: auto; display: block;">

        <img src="/assets/images/2026-07-17-the-energy-society/baseline-energy-spent.png"
             alt="Mean agent energy over time in the cooperative setting"
             style="width: 100%; height: auto; display: block;">
      </div>

      <div style="margin-bottom: 1rem; font-weight: 700; font-size: 1.8rem; line-height: 0;">
        No Size Penalty
      </div>

      <div style="display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1rem;">
        <img src="/assets/images/2026-07-17-the-energy-society/no-size-penalty-energy-over-time.png"
             alt="Average energy spent per round in the competitive setting"
             style="width: 100%; height: auto; display: block;">

        <img src="/assets/images/2026-07-17-the-energy-society/no-size-penalty-energy-spent.png"
             alt="Average energy spent per round in the cooperative setting"
             style="width: 100%; height: auto; display: block;">
      </div>
    </div>

    <div style="margin-top: 0.75rem; font-style: italic; color: #555; text-align: center;">
      Results for the baseline and no size penalty experiments. The plots on the left show mean agent energy over time where the shaded bands show the standard error of the mean across seeds. The plots on the right show average energy spent per round per agent.
    </div>
  </div>
</div>


Through further ablation studies we found that:
- **The objective** agents are prompted with affects donation behavior and division of labor (smaller agents generally attempt more hard jobs and larger agents less hard jobs when shifting from the competitive to the cooperative setting).
- **The discussion phase** affects coordination and agent ambition in terms of difficulty of attempted jobs.
- **Memory** affects risk calibration (without memory agents attempted substantially more hard jobs).
- **Model size** seems to affect efficiency.

We lastly investigated the recommendations agents made in the discussion phase in the competitive and cooperative settings.
This revealed signs of agents acting more selfishly under the competetive objective. In this setting,
agents were more frequently recommending other agents to attempt the same job, which is often disadvantageous as the reward is split.
They also still asked other agents to donate energy to them, despite this having little to no advantage for the other agents under the competitive objective.
Finally, agents seemed to be slightly more inclined to follow their own recommendations in the competitive setting.

{% include figure.html path ="/assets/images/2026-07-17-the-energy-society/recommendation-metrics.png" caption="Overview of the meassured metrics for the discussion phase. A: How often an agent recommended other agents to attempt the same job. B: How often an agent recommended donations to itself from others. C: How often agents followed their own recommendations. D: How often agents followed recommendations from other agents."%}


While *The Energy Society* offers an initial glimpse into how the environment can shape the social dynamics of large language models,
we have only begun to scratch the surface of what these multi-agent simulations can teach us. The path ahead is full of unexplored directions.

---

Based on:
Lucas Bergholdt Hansen, Federico Torrielli, Filippo Tonini, Lukas Galke Poech (2026) [The Energy Society: A Simulation Environment for Studying Agent Cooperation under Survival Pressure](https://arxiv.org/abs/2607.14865). Accepted for AI Transparency Conference 2026.

---

## References

[^0]: Chen, W., Su, Y., Zuo, J., Yang, C., Yuan, C., Chan, C.-M., ... & Zhou, J. (2023). AgentVerse: Facilitating Multi-Agent Collaboration and Exploring Emergent Behaviors. arXiv preprint arXiv:2308.10848.

[^1]: Park, J. S., O'Brien, J., Cai, C. J., Morris, M. R., Liang, P., & Bernstein, M. S. (2023). Generative Agents: Interactive Simulacra of Human Behavior. In Proceedings of the 36th Annual ACM Symposium on User Interface Software and Technology (UIST '23). 

[^2]: Dai, G., Zhang, W., Li, J., Yang, S., Ibe, C. O., Rao, S., Caetano, A., & Sra, M. (2024). Artificial Leviathan: Artificial Leviathan: Exploring Social Evolution of LLM Agents Through the Lens of Hobbesian Social Contract Theory. arXiv preprint arXiv:2406.14373.