# My first full-stack project -- promptllery

---
title: "My First Full-Stack Project — Promptllery"
date: 2025-03-30
tags: ["prompt", "fullstack", "supabase", "react"]
---

## Why I Decided to Build a Full-Stack Project

Honestly, the motivation wasn't pure tech passion — it was anxiety from comparison. I saw classmates with polished full-stack projects, while I had only built algorithm demos or component pages.

So I forced myself to make something that *looks* like a full-stack product.

On the first day I broke down:  
What is JSX? What’s the deal between Vite and React? Why am I getting `@babel/preset-react` errors in testing? What's the difference between Supabase and Node.js?

**I knew nothing — but I still started.**

That’s why I decided to document every clueless moment. Even if it’s not useful to others, it’s proof that: *I really walked this path*.


## Why This Project?
---

While working on another project (Herthspace), I came across several prompt-sharing sites like [PromptHero](https://prompthero.com/) and Prompt Playground.

It hit me:

> “If they can build such fun AI content platforms... why can’t I make one too?”

But what truly pushed me was seeing friends with end-to-end projects — frontend + backend + login + database — all fully connected. I wanted one too. I was *jealous*. 👀

Instead of copying, I started comparing what those sites actually do:

| Site        | What inspired me             |
| ----------- | ---------------------------- |
| PromptHero  | Prompt categorization + gallery → I want "Like" and "Save" |
| PromptLand  | Prompt testing + model preview → I want "built-in model test" |
| Pinterest   | Card-style UI → I want an aesthetic, fun, social-ish experience |

A lot of prompt sites look good, but feel passive. I wanted a site people *actually want to save prompts from*.

So Promptllery was born:  
A fun little prompt playground where:
- Good prompts can be **saved**
- Liked ones can be **liked** and shared
- Users can **test** prompts inline
- No GPT-4? Use **share links and QR** so no tokens are wasted!



## Thinking *Backwards* from Features to Stack
---
At this point, I worked *backwards* to choose my stack:

- I want gallery-style layout → **React + Tailwind**
- I want to store prompts → **Supabase**
- I want interactivity (like/save) → **useState/useEffect + Supabase**
- I want prompt testing → **OpenAI API (GPT-3.5 only)**
- I want to save token costs → **shareable links**

Eventually Promptllery became my playground — not to show off tech skills, but to practice product thinking.



## Why This Tech Stack?
---
I didn’t choose the stack intentionally — I just followed what others were using.  
But I slowly realized: this is the **perfect beginner-friendly "frontend-led" stack**.

| Tech               | What it is                  | Why it fits Promptllery                                 |
| ------------------| --------------------------- | -------------------------------------------------------- |
| **React**         | JS library for UI            | Each prompt is a component; like/save = reactive state  |
| **JSX**           | HTML inside JS               | Makes component building fast & intuitive               |
| **Tailwind CSS**  | Utility-first CSS            | Perfect for rapid design and layout                     |
| **Vite**          | Build tool                   | Super fast dev server, built for modern frameworks      |
| **Supabase**      | Backend-as-a-service         | No backend code needed, but still have DB & Auth        |
| **Jest + RTL**    | Testing framework + helpers  | For ensuring component logic works as expected          |
| **Vercel**        | One-click deployment         | Perfect for student / solo projects                     |



### Why not MERN?

Because I want to focus on **product logic and user experience**, not API/infra.

| Stack        | MERN (Mongo + Express) | My stack (React + Supabase)         |
| ------------ | ---------------------- | ---------------------------------- |
| Backend Dev  | You write everything   | Built-in API, no backend needed     |
| Database     | NoSQL (MongoDB)        | SQL (PostgreSQL, stable)            |
| Who it's for | Backend-focused devs   | MVP builders, frontend-heavy people |



## What I Actually Built
---
I started from zero — literally.

| Module                 | Description                                  | Implementation Details                                                |
| ----------------------| -------------------------------------------- | --------------------------------------------------------------------- |
| Prompt Card Gallery    | List prompts with titles, tags, author       | `PromptCard` component + Tailwind layout                             |
| Upload Form            | Create new prompts with title, desc, tags    | Controlled form → `onSubmit` sends to Supabase                       |
| Like + Favorite        | Users can react to prompts                   | Toggle `useState` + update Supabase row                              |
| Search & Filter        | By keyword or tag                            | Input + `.filter()` over prompt list                                 |
| Test Prompt Inline     | Run prompt with GPT                          | Call OpenAI API and display result in frontend                       |
| Share Tools            | Copy prompt, share QR                        | `navigator.clipboard.writeText`, QR from lib                         |
| Supabase Management    | Store/query prompt info, manage likes        | `supabase.from(...).insert()` or `.update()`                         |



## Lessons from the Struggle
---
I thought this was just a “temporary practice project”,  
But it became proof of something deeper:  
> **This was the first product I really built from scratch.**

| Problem                         | The Struggle                             | How I Solved It                                               |
| ------------------------------ | ---------------------------------------- | ------------------------------------------------------------- |
| Jest not reading JSX           | “Unexpected token” with `<App />`        | Installed `@babel/preset-react` + created `.babel.config.js` |
| Node version mismatch          | Packages required Node 18+               | Used `--force` first, switched later                          |
| Tailwind too verbose           | `className` chains were overwhelming     | Started using `clsx()` and layout planning                    |
| Upload form bugged             | Wrong data or no state update            | Used controlled inputs + `useEffect` properly                 |
| Vercel config confusion        | Didn’t know about Git linking or builds  | Learned `.env`, Git setup, and build settings                 |



## Final Takeaways
---
Before this project, I only admired what others had built.  
Now I know:

- Prompt sites can be built solo  
- Supabase is beginner-friendly  
- Testing is annoying but necessary  
- Debugging is a superpower  
- There’s a huge gap between “using” and “understanding”  
- I can decide what a product looks like — not just follow others

This isn’t a show-off piece. It’s my **first real tech journey** from:
idea → research → design → build → debug → write → deploy.

And I’ll never forget this feeling.


## 🎥 Demo + User Flow
---
I deployed it online (via [Vercel](https://promptllery.vercel.app/))  
and recorded a short video to walk through the full user experience — from uploading prompts to sharing them.



### 1. Homepage (Not Logged In)

![Homepage — Not Logged In](/static/promptllery/1.png)  
🔒 Before logging in, the homepage shows a search bar and a reminder: users need to log in to upload or interact with prompts.


### 2. Homepage (With Public Prompts)

![Homepage with Public Prompts](/static/promptllery/2.png)  
✨ Once logged in, prompts are displayed as cards with title, tags, and quick actions like "Copy", "Like", and "Test".


### 3. Login Page

![Login Page](/static/promptllery/3.png)  
🔐 Basic email + password login form using Supabase Auth. Authenticated users can upload and like prompts.

### 4. Upload Prompt Page

![Upload Prompt](/static/promptllery/4.png)  
📝 Authenticated users can upload new prompts with detailed metadata — title, description, tags, examples, usage tips.


### 5. Prompt Ranking Page

![Prompt Ranking Page](/static/promptllery/5.png)  
🏆 Shows the most liked or latest prompts. Each entry shows the prompt title and like count.

---

### 6. Register Page

![Register Page](/static/promptllery/6.png)  
🆕 Simple registration form for new users. Supabase handles user creation and secure password storage.


### 7. Prompt Details + Inline Testing

![Prompt Detail + Test Input](/static/promptllery/7.png)  
💡 On the prompt detail page, users can:
- View full prompt text  
- Choose GPT model (default is GPT-3.5)  
- Enter custom input  
- Click "Run Prompt" to test directly  


### 8. Prompt Detail + QR Sharing

![Prompt QR Sharing](/static/promptllery/8.png)  
📲 Every prompt has a shareable QR code and a “Copy” feature, making it easy to send prompts to friends or open in another device.


### 9. Prompt Test Result

![Prompt Test Result](/static/promptllery/9.png)  
🧠 After clicking “Run Prompt”, the GPT-3.5 model returns a live result in the frontend. This allows users to preview how a prompt performs before using it in their own tools.


### 🎬 Full Demo Video

🎥 [Watch full demo video](/static/promptllery/demo.mp4)



## 🔮 What's Next?
---
- **From solo to social**  
  Add user accounts, profiles, and public prompt collections.  
  Let users follow others, comment, and build a prompt culture.

- 🗃️ **From gallery to ecosystem**  
  Add categories, remixable prompt templates, and custom tags.  
  Build a real prompt *library*, not just a showcase.

- **From testing to co-creation**  
  Let users *build prompts together*:  
  one writes the setup, another refines the wording, a third gives sample outputs.

- **From passive use to community-driven feedback**  
  Weekly prompt picks, upvoting systems, feedback for creators.  
  Turn prompts into a living dialogue, not static text.

- **From student project to long-term playground**  
  Eventually, maybe even invite contributors.  
  Open source parts, build an API, explore monetization for top creators?

---

> Promptllery is not just a website — it’s my first step into building something *others might actually use*.  
> Hope it becomes something meaningful and fun, even if it started out of jealousy.
