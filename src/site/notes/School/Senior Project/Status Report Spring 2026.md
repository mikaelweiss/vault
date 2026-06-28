---
{"dg-publish":true,"permalink":"/school/senior-project/status-report-spring-2026/","dg-note-properties":{}}
---

# Senior Project Weekly Status Report

**Student Name:** Mikael Weiss
**Email:** wei22003@byui.edu
**Phone or Zoom Contact:** (307) 226-5183
**Project Title:** AskPip
**Team Members (if applicable):** N/a
**Project Description:** An AI tutor meant to help students more easily find course information and receive help on homework in the way a tutor would help.
**Technologies Used:** Html, css, tailwind, JavaScript, TypeScript
**GitHub URL:** https://github.com/mikaelweiss/ask-pip
(Add MountainDad as collaborator)

## Week 1: 4/25/26
On schedule
## Week 2: 5/2/26
- **Overall Status** (on-schedule, behind, ahead): On-schedule
- **Hours worked this week:** 2
- **Total hours worked so far:** 2
- **Total hours anticipated at completion:** 126
- **Accomplishments:** Built out plan
- **Challenges:** None yet. Just made plan
- **Plans / Goals for next week:** Get started
- **SPED Talk Insight:** none yet. no talks yet.
- **Other comments for the instructor:** none

---

## Week 3: 5/9/26

- **Overall Status** (on-schedule, behind, ahead): On schedule
- **Hours worked this week:** 8
- **Total hours worked so far:** 10
- **Total hours anticipated at completion:** 126
- **Accomplishments:** I was able to get auth, stripe, and the sidebar all set up. All the scaffolding for the project is ready, so now I can start focusing on the user experience
- **Challenges:** I had to learn a new WTX framework that's used for web extensions. It's cool though 'cause I can potentially release it for Safari and Firefox as well with just a little extra config.
- **Plans / Goals for next week:** Get the chat experience working so users can chat with the AI
- **SPED Talk Insight:** This video was great! I the way Ethan explained AI, and he did it in a way that was super simple, clear, and easy to understand. This applies to me because AskPip will be using a lot of AI, so understanding it a little more is very helpful.
- **Other comments for the instructor:** None at this time

---

## Week 4: 5/16/26

- **Overall Status** (on-schedule, behind, ahead): On schedule
- **Hours worked this week:** 5
- **Total hours worked so far:** 15
- **Total hours anticipated at completion:** 126
- **Accomplishments:** Got the chat area working. Created a design system and theme. I also got Markdown rendering and math rendering set up inside of the chat interface, which is super helpful.
- **Challenges:** Getting an AI to generate UI that doesn't look like it was made by AI takes a ton of effort. I also wanted to use OpenRouter, which I think I'll not use. It's too slow. I want the responses to be faster. I'll probably use Fireworks.ai instead. It's a lot faster and has a more clear privacy policy than OpenRouter.
- **Plans / Goals for next week:** **Canvas DOM Scraping** Build the content script that detects what type of Canvas page the user is on - dashboard, course home, assignment, quiz, syllabus, modules, grades, discussions, announcements. Implement DOM scrapers for assignment pages (title, description, due date, points, rubric) and course-level pages (course name, syllabus content, module list). Set up Chrome extension messaging between the content script and side panel to pass the scraped data. Add a context indicator in the UI showing what page and course the AI currently knows about. By the end of next week: the extension correctly identifies Canvas page types and pulls out meaningful content from each.
- **SPED Talk Insight:** It was really cool to learn about abstract syntax trees, especially because that's something that I use all the time in my code editors. And so understanding how it works in practice is really exciting.
- **Other comments for the instructor:** none at this time

---

## Week 5: 5/23/26

- **Overall Status** (on-schedule, behind, ahead): on-schedule
- **Hours worked this week:** 5
- **Total hours worked so far:** 20
- **Total hours anticipated at completion:** 120
- **Accomplishments:** I got the chat set up with a context engine. Whenever you send a message, AskPip checks what kind of page you're on, and then calls the Canvas API to gather whatever context is available for that page.
- **Challenges:** This was a lot of work, and I think that the biggest challenge was organizing all the work an doing it in the simplest way that it can be done.
- **Plans / Goals for next week:** Build out assignment help feature where users are able to ask the agent for help in a way that it would help them similar to a tutor.
- **SPED Talk Insight:** I learned that bycript is used for password hashing! It was an enjoyable presentation.
- **Other comments for the instructor:** none at this time

---

## Week 6: 5/30/26

- **Overall Status** (on-schedule, behind, ahead): on-schedule
- **Hours worked this week:** 5
- **Total hours worked so far:** 25
- **Total hours anticipated at completion:** 120
- **Accomplishments:** I got it set up so that when you're inside of an assignment page, then the AI responds as a tutor would, helping guide the student without giving the answer directly.
- **Challenges:** I think with this, the hard part is figuring out how to have the AI be helpful without giving the answer, because that is kind of vague instructions. So that was interesting to figure out how to work with.
- **Plans / Goals for next week:** Add more harness things with tool calls where the agent can fetch more information from Canvas if needed, render math, things like that.
- **SPED Talk Insight:** It was really awesome to learn about the idea of responsive design and animations in design. I thought it was quite interesting.
- **Other comments for the instructor:** None at this time.

---

## Week 7: 6/6/26

- **Overall Status** (on-schedule, behind, ahead): on-schedule
- **Hours worked this week:** 10
- **Total hours worked so far:** 35
- **Total hours anticipated at completion:** 120
- **Accomplishments:**
    1. Migrated to Fireworks.ai. Holly cow, it's so much better.
    2. LaTeX renderring 🔥
    3. Added ability for agent to run tool calls
    4. Added a bunch of tools for the agent
    5. Added ability for the agent to read documents/files attached to things
- **Challenges:** Switching from Grok to fireworks was a bit of work. Also, tool calling is always tough to get right. 
- **Plans / Goals for next week:** Test out the pdf stuff and fix any issues. Make sure stripe is all working.
- **SPED Talk Insight:** I loved the insights about cash-on-right versus cash-on-read, especially how there are clear advantages to cash-on-right specifically when working with AI APIs. 
- **Other comments for the instructor:** None at this time

---

## Week 8: 6/13/26

- **Overall Status** (on-schedule, behind, ahead): on-schedule
- **Hours worked this week:** 15
- **Total hours worked so far:** 40
- **Total hours anticipated at completion:** 120
- **Accomplishments:**
    - Chat attachments - can add images and any time of unicode file
    - Added toggle to show/hide assignment info in Canvas
    - Made AI less verbose
    - User can now delete conversations
    - User can now edit messages and branch between messages (including editing images/files attached to a message)
    - Turn error handling
- **Challenges:**
    - Getting the AI to perform as expected. It was originally too verbose, but it wouldn't give you the final answer. Now it's less verbose, but will sometimes just directly give you the answer.
- **Plans / Goals for next week:**
    - AI File Comprehension - close to set up, but currently timing out for some reason. Need to fix.
    - Verify that Stripe is set up correctly
- **SPED Talk Insight:** It was really awesome to learn about API's and how integral they are to software development.
- **Other comments for the instructor:** None at this time

---

## Week 9: 6/20/26

- **Overall Status** (on-schedule, behind, ahead):
- **Hours worked this week:** 25
- **Total hours worked so far:** 65
- **Total hours anticipated at completion:** 120
- **Accomplishments:**

**Writing & documents**
- Built a built-in document editor so students can write essays right inside the extension
- Added the ability for Pip (the AI) to create and rewrite documents for you
- Pip can now edit your document live as you chat with it
- Added one-click submitting of a finished document straight to Canvas
- Added ready-made academic formatting styles (so papers look properly formatted)

**Web search**
- Gave Pip the ability to search the web during a chat, so answers can pull in current info

**Signing in**
- Added a sign-in button directly in the sidebar
- Made the settings page the main place to sign in, and kept your login in sync across the extension

**Files & assignments**
- Added the ability to pull in files from your previous assignments
- Fixed problems with uploading files
- Fixed Pip getting confused between the Canvas page and the document editor

**Chat improvements**
- Conversations now stay tied to the specific course and assignment you're working on
- You can now see Pip's reasoning and the tools it's using as it works
- Added a stop button to cancel Pip mid-response

**Legal**
- Privacy Policy
- Terms of service
- Landing page

**Dev**
- Added PostHog Analytics

- **Challenges:** I had some issues where pip would want to create a completed file instead of a template file. I also had some issues accessing certain files that were linked. For example, the template file for this weekly status report. I've also had some issues with getting web search set up reliably.
- **Plans / Goals for next week:**
    - Onboarding
    - Better empty states
    - Chat hardening
- **SPED Talk Insight:** I didn't realize there was so much going on under the hood to get geocoordinates working correctly, so that was really awesome to learn about and understand how to geocode and reverse geocode reliably both ways.
- **Other comments for the instructor:** none at this time

---

## Week 10: 6/27/26

- **Overall Status** (on-schedule, behind, ahead): on-schedule
- **Hours worked this week:** 20
- **Total hours worked so far:** 85
- **Total hours anticipated at completion:** 120
- **Accomplishments:** 
    - Finished testing the pro plan and payment handling
    - Added analytics
    - Tied each conversation to the specific course and assignment a student is working on, so chats stay organized
    - Cleanup code duplication
    - Updated create file button to use AI to create the file either from an existing template or a generated template based on the assignment instructions
    - Added buttons to download and replace files
    - Fix some issues where files sometimes weren't auto-saving
    - Let Pip see and pull from documents across all assignments, not just the current assignment
    - Better web search UI/UX
    - Fix issue where web search would sometimes fail
    - Add more logging
    - Better error handling
    - Prep for publishing
    - Screenshots
    - Submitted to chrome store for publication
    - Improved onboarding
    - Added chat hardening
    - Made better empty states
- **Challenges:** I was originally having some issues where I wasn't seeing any logs in any of the terminals whenever there were issues, and that was making it difficult to debug things. And so, adding logging helped a lot with that.
- **Plans / Goals for next week:** 
    - Add version management to the document editor
    - Make a place to view all your files
- **SPED Talk Insight:** I liked learning about IPC. I've used IPC before with Electron, but I hadn't before thought of it as something that could be use to work between languages. For example, elixir and rust. That was very insightful.
- **Other comments for the instructor:** None at this time

---

## Week 11: 7/4/26

- **Overall Status** (on-schedule, behind, ahead):
- **Hours worked this week:**
- **Total hours worked so far:**
- **Total hours anticipated at completion:**
- **Accomplishments:**
- **Challenges:**
- **Plans / Goals for next week:**
- **SPED Talk Insight:**
- **Other comments for the instructor:**

---

## Week 12: 7/11/26

- **Overall Status** (on-schedule, behind, ahead):
- **Hours worked this week:**
- **Total hours worked so far:**
- **Total hours anticipated at completion:**
- **Accomplishments:**
- **Challenges:**
- **Plans / Goals for next week:**
- **SPED Talk Insight:**
- **Other comments for the instructor:**

---

## Week 13: 7/18/26

- **Overall Status** (on-schedule, behind, ahead):
- **Hours worked this week:**
- **Total hours worked so far:**
- **Total hours anticipated at completion:**
- **Accomplishments:**
- **Challenges:**
- **Plans / Goals for next week:**
- **SPED Talk Insight:**
- **Other comments for the instructor:**
