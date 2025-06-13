![r3shape](https://github.com/r3shape/luna/blob/main/external/__data__/assets/textures/logo.png)  
# r3shape / essays

This repo collects technical essays, notes, and research written as part of the ongoing development of r3shape and it's ecosystem of open-source software.

Each essay is written in Markdown, version-controlled, and intended as both a personal archive and public reference. Writing these is part of a broader process: study deeply, build deliberately, and document the ideas that shape the work.

---

## Published

- **[Elegant Design: Dispatch Tables in C](ed-dispatch-tables.md)**  
  Function pointers, runtime polymorphism, and manual vtables in C.
- **[Elegant Design: Tagged Unions In C](ed-tagged-unions.md)**  
  Variable data, dynamic typing and type safety.

---

## Upcoming
- **Frontend: A Religion Of Ignorance**
- **Building Tools, Not Bottlenecks: A look at r3make**

## About r3shape

r3shape is an organization focused on building foundational tools for developers, with an emphasis on clarity, extensibility, and simplicity.

Current projects include:

- **r3make** — a lightweight, JSON-configured CLI build tool for C
- **NetSpeak** - a minimalistic network programming framework, leveraging JRPC2 (custom message spec planned)
- **Luna** — a pure C 2D+3D runtime-based graphics engine, with native plugins and hot-reloads, leveraging OpenGL (future api expansion planned)
- **SSDK** — a modular runtime library offering APIs for memory, math, generics, file I/O, ECS, data structures, and more
- **Blakbox** - a high-level SDL2 framework based on the Pygame library offering app level orchestration, a simple and robust resource pipeline, and serializable scene, world, and object classes
The essays in this repo often trace the research, experiments, and ideas that inform these tools.

---

## License
All content in this repository is licensed under the [MIT](LICENSE) license.
