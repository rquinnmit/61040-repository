# Assignment 1

# Domains

- Interactivity of video games [CHOSEN]
- Video game generation
- Recommending music for individual song tastes
- Social coordination for college events
- Code generation [CHOSEN]
- Medical training data
- Simulation training for machines
- DJing [CHOSEN]
- Marketplaces for specialized AI agents
- Clinical documentation

---

## Interactivity of video games

I’ve loved playing open-world games because of the unique interactions you can have with NPCs, world exploration, and missions. Some of these games include Red Dead Redemption II and GTA V. Once I run out of interactions to experience in a game, I usually quit because the excitement drains.

### Problems
- **Limited number of player interactions with NPCs [CHOSEN] **  
  - **Explanation:** LLMs are capable of generating conversations. If an NPC within a game were to have an LLM scripted into its functionality, it would allow players to enter custom prompts and receive custom interactions in-game.
- **Limited mission generation for open-world games**  
  - **Explanation:** This idea would be extremely hard to implement. Games range from being 2D to 3D with extremely high graphics quality, making it hard for actual missions to be generated straight from LLMs.
- **Limited character customization in a game setting**  
  - **Explanation:** This idea would be extremely hard to implement. Games range from being 2D to 3D with extremely high graphics quality, making it hard for actual character qualities to change.

### Stakeholders
- **Game developers:** Needed to test the development of NPCs using our project.
- **Video game users:** Needed to test the functionality and enjoyability of our project.
- **AI developers:** Can evaluate how certain models can be used more effectively to produce better interactions.

### Evidence
- There is a huge audience, since NPC-using open-world games have a massive reach (GTA V has sold ~215M copies, Red Dead Redemption 2 with ~77M). [Wikipedia: Grand Theft Auto V](https://en.wikipedia.org/wiki/Grand_Theft_Auto_V)
- Other studios are prototyping LLM NPCs. Examples are Inworld and Ubisoft. [Ubisoft generative AI prototype for NPCs](https://news.ubisoft.com/en-gb/article/5qXdxhshJBXoanFZApdG3L/how-ubisofts-new-generative-ai-prototype-changes-the-narrative-for-npcs)
- Many AI NPCs are showing latency and consistency issues. [NVIDIA ACE in-game inference SDK](https://developer.nvidia.com/blog/bring-nvidia-ace-ai-characters-to-games-with-the-new-in-game-inference-sdk/)
- Tools are being created for AI NPCs that could be utilized. [NVIDIA ACE for Games](https://developer.nvidia.com/blog/generative-ai-sparks-life-into-virtual-characters-with-ace-for-games/)
- Inworld AI, a large player in this field, has a partnership with Microsoft/Xbox. [Microsoft/Xbox + Inworld AI partnership](https://developer.microsoft.com/en-us/games/articles/2023/11/xbox-and-inworld-ai-partnership-announcement/)
- Research from Stanford on the ability for LLM agents to form plans, memories, and social behaviors like NPCs would. [Stanford HAI article](https://hai.stanford.edu/news/computational-agents-exhibit-believable-humanlike-behavior)
- Humanlike simulation with 1,000 agents that replicate the attitudes and actions of real humans. [arXiv:2411.10109](https://arxiv.org/abs/2411.10109)
- Steam requires games to disclose when they are using AI. [GameSpot coverage](https://www.gamespot.com/articles/game-developers-will-need-to-disclose-ai-use-on-steam/1100-6520235/)
- AI Dungeon scaled to millions of users but ran into moderation issues. [MineDojo](https://minedojo.org/)
- Research on generative agents simulating humanlike behavior. [arXiv:2304.03442](https://arxiv.org/abs/2304.03442)

### Features
- **Integration:** Plugin that integrates with a game development platform (Unity, Unreal) so that any developers (Indie or large companies) can utilize AI NPC protocol. This increases reach and makes it easier to utilize for a wide variety of game types.
- **Dialogue Generation:** Use standard LLM models (GPT-5, Gemini 2.5 Flash, etc.) to generate dialogue for NPCs. Although more expensive, this allows for dynamic interactions that create a better gaming experience.
- **Dialogue to Action:** For each dialogue interaction, output matrix scores that help developers associate certain responses with future missions or actions. For example, if a player has a hostile interaction with an NPC, the score can map to actions like the NPC fighting or becoming hostile as well. This makes the project adaptable and useful for developers.

---

## Code generation

I’ve been using code generation apps to help me plan, write, and test any apps that I build. There have been some really cool advancements in the field (Cursor, Claude Code, Lovable, etc.) that have changed the way code is written. I like the terminal design choice of Claude Code, but also like the way that Lovable is a web app and allows you to generate styled frontends.

### Problems
- **Limited context windows reduce the quality of generated code [CHOSEN]**  
  - **Explanation:** With long and extensive software projects, limited context windows of singular agents create problems with how code is generated, as the agent fails to consider important context. This is widespread and constrains the field. A hierarchical system of agents that work together to spec, generate, and test code could help—though it would be very difficult.
- **Code generation is often incorrect**  
  - **Explanation:** Many research scientists at large companies (OpenAI, Anthropic, etc.) are already working on this problem. It is mainly rooted in model development and likely cannot be solved by an app alone.
- **API costs often get really expensive for large projects**  
  - **Explanation:** We cannot control the model costs; those are set by the companies making the models.

### Stakeholders
- **Software engineers, researchers, college students.**  
  - Could reduce the need for extensive QA by engineers.  
  - Helps students generate software projects.  
  - Researchers can explore and differentiate the approach.

### Evidence
- Research on chain-of-agents architectures by Google. [Chain of Agents (Google Research blog)](https://research.google/blog/chain-of-agents-large-language-models-collaborating-on-long-context-tasks/)
- Benchmark that signals generation issues when an answer sits in the middle of the context. [“Lost in the Middle”](https://cs.stanford.edu/~nfliu/papers/lost-in-the-middle.arxiv2023.pdf)
- The market for open-source code is massive. [Linux kernel surpasses 40M LOC](https://www.stackscale.com/blog/linux-kernel-surpasses-40-million-lines-code/)
- Debate over the quality of LLMs in producing code; many developers question whether LLMs are worth using. [Stack Overflow discussion](https://stackoverflow.com/questions/77695979/retrieval-augmented-generation-vs-llm-context)
- Transformers have standard attention scaling with O(n²); can be made more efficient. [arXiv:2004.05150](https://arxiv.org/abs/2004.05150)
- Many models push for long context windows, but they’re expensive and often slow. [Anthropic pricing](https://docs.anthropic.com/en/docs/about-claude/pricing)
- Long-context models can under-attend to mid-document facts; position matters. [arXiv:2307.03172](https://arxiv.org/abs/2307.03172)
- LLMs often exhibit positional bias in long-context settings. [arXiv:2410.23609](https://arxiv.org/abs/2410.23609)
- Long-context retrieval is improving over time with more advanced models and architecture. [Anthropic: Claude 2.1 prompting](https://www.anthropic.com/news/claude-2-1-prompting)
- Multi-agent methods (iteration and verification) often improve code generation. [arXiv:2304.05128](https://arxiv.org/abs/2304.05128)

### Features
- **Hierarchical Agents:** A system of agents that work together to test, create, and iterate over generated code (somewhat similar to Grok 4 Heavy), enabling more efficient context processing.
- **Web App:** The agentic framework exists in a web app (similar to Lovable, Bolt.new), broadening reach since many start with web-based development platforms.
- **Prompting and Generation:** Users prompt agents with a task, can communicate during a prompting phase, and then see code generated live (similar to Lovable and Bolt.new), putting users in the passenger seat for development.

---

## DJing

I’ve been DJing for a couple years and think it’s a great way of blending music together, even if two songs aren’t similar whatsoever. Most of the music I DJ has been Pop and Hip Hop, but there are tons of different genres, keys, and BPMs that need to be taken into account when mixing songs and deciding which song to mix next.

### Problems
- **Limited capabilities of AI to mix two songs**  
  - **Explanation:** Software currently exists (e.g., Spotify DJ) that does the same thing. It’s also a large focus in the AI music generation field, so it would be hard to differentiate.
- **Difficulties for DJs to choose their next song**  
  - **Explanation:** Ties with the mixing-two-songs space, which is already heavily contested.
- **Difficulties for DJs to find new songs that match their style [CHOSEN]**  
  - **Explanation:** Song databases (BPM, style, key, genre, etc.) exist and can be utilized more efficiently using AI. This is a medium-sized issue for DJs but makes finding suitable tracks much easier.

### Stakeholders
- **Professional/Amateur DJs:** Needed to test whether recommendations are high quality.  
- **Song database sites:** Needed to provide backbone metadata for recommendations.  
- **Spotify (or other streaming) users:** Can judge the effectiveness of recommended songs when listening to a DJ set.

### Evidence
- The Electronic/Dance music market is large and active. [IMS Business Report 2024 (Mixmag MENA)](https://mixmagmena.com/read/ims-business-report-2024-news)
- The market for DJ equipment is huge; DJs pay for tools that benefit their abilities. [Business Research Insights](https://www.businessresearchinsights.com/market-reports/dj-equipment-market-102781)
- Much recorded music revenue comes from streaming; these are the songs our AI will leverage. [Reuters](https://www.reuters.com/business/media-telecom/music-revenues-rise-again-2024-boosted-by-streaming-subscriptions-report-shows-2025-03-19/)
- Thousands of new tracks are uploaded daily, making selection difficult. [Music Business Worldwide](https://www.musicbusinessworldwide.com/there-are-now-120000-new-tracks-hitting-music-streaming-services-each-day/)
- Most songs are buried and receive little to no listening. [Music Business Worldwide](https://www.musicbusinessworldwide.com/158-million-tracks-1000-plays-on-streaming-services/)
- Some DJs report spending a large amount of time searching for songs. [Reddit thread](https://www.reddit.com/r/DJs/comments/1gy70cm/realistically_how_many_hours_do_you_spend_each/)
- Harmonic mixing (matching BPMs, keys, etc.) is commonplace within DJ platforms. [Wikipedia: Harmonic mixing](https://en.wikipedia.org/wiki/Harmonic_mixing)
- Many criticize Spotify DJ for being impersonal. [Wired](https://www.wired.com/story/spotify-ai-dj/)
- Recommendations for songs exist but are primitive.  
- Beatsource Streaming allows DJs to explore categories but lacks AI tooling. [Beatsource/Beatport Streaming](https://stream.beatport.com/)

### Features
- **Information Scraping:** Extensively scrape song information (e.g., BPM, key, genre) so the AI has a broad pool to select from, enhancing recommendations.
- **Prompting:** Allow users to input a song file, which is analyzed automatically to inform matches.
- **Integration:** Integrate into DJ platforms so DJs can find new songs in real time.

---
