<img alt="cybrcore" src="https://raw.githubusercontent.com/cybrcore/cybrcore/refs/heads/main/assets/repo-banners/cybrcore-banner.png">

<p align="center">
  <a href="https://stand-with-ukraine.pp.ua/">
    <img alt="standwithukraine" src="https://img.shields.io/badge/Support-Ukraine-FFC93C?style=flat-square&labelColor=07689F">
  </a>

# What is `cybrcore`
## tl;dr
Lucid design system and philosophy. Custom color palette, wallpapers, deliberate typography. 20+ redesigned applications. Open source under GPL-3.0.

## Intro
Design is function followed by form. The function solves a problem, the form makes the solution understandable. There is another definition I keep coming back to: "design is applied art". In daily practice, we were bullied into thinking that design is completely removed from art and that we are not artists. In the end, clients pay for solutions, not for an „useless“ art pieces, right? I think this precise line of thinking is not only incredibly dangerous, but one of the root causes of why design keeps failing us. As designers and as users.

## The Problem
Contemporary design is a tool of exploitation and enslavement. It conditions us into passive-submissive acceptance of the world as it is, and strips us of the ability to imagine alternatives. It insists upon itself. It enforces a worldview where owning and making something ours in the digital landscape begins and ends with uploading a profile picture and at the very most, choosing an accent colors from a limited palette, effectively killing our self-expression. We are forced to endure endless barrages of banners, popups, springy animations that take forever to finish and micro-interactions fighting for the last remnants of attention.

Design discourse is fed by "best practices" grind culture that enforces a preferred default state. The design we are left with is neutered, without any distinct smell or taste. Round, smooth, sterile, calculated to be inoffensive, forgettable, but above everything: frictionless and deliberately manipulative. Yet it still has a personality -- a fake one. Dark patterns and addictive interaction loops erode our capacity to think, create, discuss and act freely. It is the absence of any meaningful friction that makes us dependent, complacent, and scared of anything that takes any effort to do. We have the most powerful machines in history, yet the tools we use daily are inexplicably slow, fragile, and patronizing, while at the same time feeding on our creativity, slowly but surely eliminating the very will for us to create.

We surrendered agency in exchange for a completely false sense of safety, efficiency and ease. We willingly let the parasites devour us. We forgot how to be brave, how to experiment, how to play. We forgot how to fight.

This critique is not new. I'm continuing what Rams, Monteiro, Bauhaus, Swiss typographers and the brutalists were saying a long time ago. I'm not the first to notice that something is broken. But noticing hasn't fixed it.

## The Roots
Early computing systems placed function uncompromisingly above form, because computation was very limited. They respected users as thinking, curious, learning beings. The goal was not to lower barriers to entry and please everyone. The goal was obedience of the system to the user's commands, in expected and predictable manner. Later, the systems introduced icons. Thoughtfully. They helped identify commands not just by text and position, but also by shape, reducing cognitive load without sacrificing function. It was in this era the famous UNIX philosophy and it's most known commandment „Write programs that do one thing and do it well“ was born. It has become one of the mantras of this project.

## The Solution?
`cybrcore` is built on a fundamentally radical set of assumptions. It has a strong visual identity, rooted in purpose, not polish or chrome. Function dictates form, but the form is allowed to have attitude. The system is terminal-first, going as far as modifying Graphical User Interface (GUI) applications to look like Text-based/Terminal UI (TUI) or Command Line Interface (CLI) ([read about the difference](https://itsfoss.com/gui-cli-tui/)), stripped of anything that doesn't serve the user. Animations are rare -- if used, they are snappy and quick, with minimal-to-none easing, and they communicate state, not "delight", not wasting precious time. The `cybrcolors` color palette carries semantic meaning. Every decision, documented in a design philosophy document, traces back to one question: *Is this decoration or information*?

### Base
Arch Linux. Flexible, light, fast, free. To ensure consistency throughout the system, most GUI applications are replaced with TUI and CLI tools. The only GUI apps are browser, IDE and note-taking app -- all redesigned to look and feel like they run in a terminal. It takes deliberate effort to learn, but the result is fast, responsive, and intuitive.

### Aesthetic
The aesthetic is an honest reflection of the world we are living in, "*high tech, low life*", not surface-level styling. Corporate lobbying, climate crisis, wars, genocides, wealth concentrated in the hands of a cabal of unelected fascist billionaires. This is the world our interfaces exist in, yet nothing in mainstream design acknowledges it. `cybrcore` doesn't look away, it's lucid. It is a critique of the design industry and a critique of myself, and an attempt to practice my own principles. A UI system that is honest about the world it exists in, while returning control and agency to the person sitting in front of the screen. Where it ought to be.

### Typography
A single typeface -- Geist Mono Nerd Font -- across every application. This is a deliberate commitment to the aesthetic. Early computing was monospaced. Terminals are monospaced. Developers read monospaced text for hours and work effectively with it. The trade-off might be reduced comfort in reading long-form prose until accustomed to, and that trade-off is accepted. For users who need a proportional alternative in specific contexts, `Atkinson Hyperlegible Next` can be configured as an override. The default stays.

### Friction
The friction of adapting to a new design paradigm, of learning a keyboard-driven and terminal-first system is intentional. It filters for users willing to put effort in exchange for control. That trade is the point.

The friction of manual installation and incomplete tooling is not intentional. This project is still in early-stage. The installer script is in the pipeline. Until it ships, some rough edges are practical limitations, not philosophical ones.

### Attitude
Self-indulgent decoration is noise. "Self-indulgent" points at the tendency of adding noise without discipline, consideration for others, irresponsibly, immoderately, for personal gratification. As stated above, "function dictates form, but **the form is allowed to have attitude**". The shape of the buttons, with angled top right and bottom left corners, is core part of the identity. The `cybrpaper` wallpapers provide depth through the transparency stack, they ground the system visually and help with immersion through diegetics by mimicking how ambient light gets reflected off of screens. These elements had to justify their existence against the question "*is this decoration or information?*" and they passed, because they signal to users "you're in `cybrcore`", they do coherence work, identity work, and orientation work.

### Core tenets
1. Consistency is key
2. Self-indulgent decoration is noise
3. Free Open Source Software whenever possible
4. TUI instead of GUI whenever possible
5. If using GUI, then it should look and feel like TUI
6. Minimal or none animations, minimal to no easing
7. `Geist Mono Nerd Font` as the default font, `Atkinson Hyperlegible Next` optional for long-form
8. The system obeys
