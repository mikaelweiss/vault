---
{"dg-publish":true,"permalink":"/developer/resources/phoenix-elixir/","dg-note-properties":{}}
---

Curated learning path for a developer with 5+ years experience picking up Elixir and Phoenix.

## The Learning Path

The consensus is: **learn Elixir first, then Phoenix**. Phoenix leans heavily on OTP, pattern matching, and the process model - you'll fight the framework if you skip the language. Budget ~2 weeks on Elixir fundamentals before touching Phoenix.

## Elixir (the language)

### Books
- **Elixir in Action, 3rd Edition** (Saša Jurić, 2024) - the gold standard. Covers the language, OTP, concurrency, fault-tolerance, and distributed systems. Written for devs with production experience in other languages. Print purchase includes free eBook. [Manning](https://www.manning.com/books/elixir-in-action-third-edition) | [Amazon](https://www.amazon.com/Elixir-Action-Third-Sa%C5%A1a-Juric/dp/1633438511)
- **Programming Elixir >= 1.6** (Dave Thomas) - solid alternative, lighter on OTP but great for getting productive fast. Targets Elixir 1.6+, still relevant for fundamentals. [PragProg](https://pragprog.com/titles/elixir16/programming-elixir-1-6/) | [Amazon](https://www.amazon.com/Programming-Elixir-1-6-Functional-Concurrent/dp/1680502999)
- **Testing Elixir** (Andrea Leopardi & Jeffrey Matthias, 2021) - covers unit tests, integration testing, OTP testing, Ecto, Phoenix, and property-based testing with StreamData. 262 pages. Targets Elixir 1.8+/Phoenix 1.3+ so API details may need checking, but the testing patterns and philosophy are still solid. [PragProg](https://pragprog.com/titles/lmelixir/testing-elixir/)

### Official Resources
- [Elixir Getting Started Guide](https://elixir-lang.org/getting-started/introduction.html) - surprisingly good for experienced devs. Skip the "what is a variable" parts, focus on the concurrency chapters.
- [Elixir Learning Resources Page](https://elixir-lang.org/learning.html) - official curated list of books, courses, and screencasts.
- [HexDocs - Elixir](https://hexdocs.pm/elixir/introduction.html) - API reference and guides.

### Talks
- **The Soul of Erlang and Elixir** (Saša Jurić) - essential talk for understanding why Elixir's concurrency model matters. Demonstrates the BEAM VM's process model and fault tolerance in a visceral way. [YouTube](https://www.youtube.com/watch?v=JvBT4XBdoUE)

### Courses
- **Pattern Matching** (Mark Ericksen / Thinking Elixir) - free, 20+ lessons across 3 modules. Covers pattern matching across data types, function declarations, and guard clauses. Includes TDD exercises. A great focused drill on the concept that makes Elixir click. [Course Link](https://thinkingelixir.com/available-courses/pattern-matching/)

## Phoenix (the framework)

### Books
- **Programming Phoenix 1.4** (Chris McCord, José Valim, Bruce Tate) - written by Phoenix's creator, Elixir's creator, and a seasoned technical author. The definitive guide. [PragProg](https://pragprog.com/titles/phoenix14/programming-phoenix-1-4/) | [Amazon](https://www.amazon.com/Programming-Phoenix-1-4-Productive-Reliable/dp/1680502263)

### Official Resources
- [Phoenix Overview](https://hexdocs.pm/phoenix/overview.html) - architecture, request lifecycle, MVC patterns. Familiar if you've used Rails or Django.
- [Phoenix Up and Running](https://hexdocs.pm/phoenix/up_and_running.html) - quickstart. As of 1.8.5 there's a one-liner: `curl https://new.phoenixframework.org/myapp | sh`
- [Phoenix Guides on HexDocs](https://hexdocs.pm/phoenix/1.8.5/) - comprehensive. Routing, controllers, contexts, Ecto, channels, deployment.

### Video Courses
- **[Pragmatic Studio](https://pragmaticstudio.com/)** - three Elixir/Phoenix courses: **Elixir & OTP**, **Full-Stack Phoenix** (58 guided exercises), and **LiveView**. Highly regarded, structured, paid.
- **Alchemist Camp** (free) - largest collection of free Elixir/Phoenix screencasts. Project-focused, assumes web dev experience.

## LiveView (the killer feature)

LiveView is Phoenix's real-time, server-rendered interactive UI library - no custom JS needed. This is probably why you're looking at Phoenix in the first place.

### Books & Courses
- **Programming Phoenix LiveView** (Sophie DeBenedetto & Bruce Tate) - the definitive LiveView book. Was PragProg's #3 bestseller in 2024. [PragProg](https://pragprog.com/titles/liveview/programming-phoenix-liveview/)
- **Testing LiveView** (German Velasco) - free video course, 16 topics covering testing rendering, interactions, forms, async assigns, LiveComponents, file uploads, JS hooks. Assumes existing LiveView knowledge. [testingliveview.com](https://www.testingliveview.com/)
- [Phoenix LiveView HexDocs](https://hexdocs.pm/phoenix_live_view) - official docs.

### Articles
- **[Fly.io Phoenix Files](https://fly.io/phoenix-files/)** - Fly.io's dedicated Phoenix/LiveView blog. Deep technical articles on real-world patterns. Active through 2026.
  - Example: [Saving and Restoring LiveView State](https://fly.io/phoenix-files/saving-and-restoring-liveview-state/) (Mark Ericksen) - using sessionStorage to persist LiveView state client-side with Phoenix's built-in encryption.

## Community + Staying Current
- [Elixir Forum](https://elixirforum.com/) - the main community hub. Active, welcoming, core team participates.
- [Elixir Hub](https://elixir-hub.com/books) - curated book/resource lists.
- [Thinking Elixir Podcast](https://thinkingelixir.com/) - weekly podcast covering the ecosystem.
- [ElixirConf talks on YouTube](https://www.youtube.com/@ElixirConf) - conference talks, good for deep dives.

## Tips

- Elixir v1.19 adds `mix help app:phoenix` - lists all modules in a Phoenix app alongside their doc summaries. Handy for navigating an unfamiliar codebase.
- `iex -S mix phx.server` starts the server with an interactive shell - great for poking at things live.

## Quick Reference

```bash
# Install Elixir (macOS)
brew install elixir

# Install Phoenix generator
mix archive.install hex phx_new

# Create a new Phoenix app
mix phx.new myapp

# Or the new one-liner (Phoenix 1.8+)
curl https://new.phoenixframework.org/myapp | sh

# Start the server
cd myapp
mix deps.get
mix ecto.create
mix phx.server

# Start with interactive shell
iex -S mix phx.server

# List all modules in a Phoenix app (Elixir 1.19+)
mix help app:phoenix
```

## What Makes Phoenix Click for Experienced Devs
- **Functional MVC** - if you know Rails/Django, the structure is familiar, but everything is immutable and pattern-matched
- **OTP/GenServer** - the concurrency model is the real unlock. Understand this and Phoenix makes total sense
- **Ecto** - the database layer. Not an ORM - it's a query DSL with changesets for validation. Explicit > magic
- **Channels + LiveView** - real-time built into the framework, not bolted on
- **Fault tolerance** - supervisors restart crashed processes automatically. "Let it crash" is a feature, not a bug
