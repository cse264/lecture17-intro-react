# Lecture 17 In-Class Activity: Trivia Night Scoreboard

Work with a partner. We'll build the first component together up front, then you'll build the rest. We'll stop twice to talk about what people found, so when you get to a **Stop** line, finish what you're running and wait for the rest of the class.

Today you're building a scoreboard for a trivia night in React. Each team gets a card with its score and some buttons, and the top of the page shows who's winning.

This repo is the same Vite + React setup we made in lecture with `npm create vite@latest`, with the demo stuff stripped out.

## What's in this repo

| File | What it is |
| --- | --- |
| `index.html` | The one HTML page. All it has is `<div id="root">` and a script tag for `main.jsx`. |
| `src/main.jsx` | Puts `App` into that `root` div. You won't need to touch it. |
| `src/App.jsx` | Your main component. Mostly TODOs right now. |
| `src/data.js` | The four teams for tonight. You'll use it in Part 1. |
| `package.json` | The `dev` script and the packages (React, Vite). |

## Getting set up

1. Run `npm install`.
2. Run `npm run dev`, then open the `localhost` link it prints (usually http://localhost:5173).
3. You should see **Trivia Night Scoreboard**. Leave the dev server running. The page updates every time you save, so you don't have to refresh.

Keep the browser's dev tools console open the whole time (right-click → Inspect → Console). React prints its warnings there, not in your terminal.

## Up front (together)

We'll make a `TeamCard` component in its own file, `src/TeamCard.jsx`, then import it into `App` and render two of them. Type along in your own files. While we do, watch for:

- Why the component's name has to start with a capital letter.
- What the `{ }` curly braces let you do in JSX.
- What happens when we click one card's button. Does the other card's score change too?

By the end, `TeamCard.jsx` will look like this:

```jsx
import { useState } from 'react'

function TeamCard({ name }) {
    const [score, setScore] = useState(0)

    return (
        <div className="team-card">
            <h2>{name}</h2>
            <p>Score: {score}</p>
            <button onClick={() => setScore(score + 1)}>+1</button>
        </div>
    )
}

export default TeamCard
```

And `App` renders it like this:

```jsx
<TeamCard name="Quizzly Bears" />
<TeamCard name="The Know-It-Owls" />
```

**The rule for today:** state only changes through its setter. Never write `score = score + 1`. React won't know anything changed, so it won't redraw the page.

## Part 1: One component, many teams (about 7 minutes)

- Give `TeamCard` a `color` prop and use it to color the card somehow (the team name, a border, whatever you like).
- Import `teams` from `data.js` and render a card for every team using `.map()`, instead of typing out `<TeamCard />` four times. Save, then check the browser console.
- Add a **−1** button. A score should never go below 0, and the button should look disabled when it can't be used. (There's an HTML attribute for that, and JSX takes it like any other prop.)

**Stop.** Four cards, one component: where does each card's score actually live? What warning showed up in the console after you used `.map()`, and what did you give React to fix it?

## Part 2: Lifting state up (about 10 minutes)

Above the cards, show this:

```
Leader: Les Quizerables (7)    Total points: 15
```

You'll hit a wall right away. `App` can't see any of the scores, because each one is locked inside its own `TeamCard`. Data in React only flows down, from parent to child.

So move the scores up:

- `App` owns the scores now. `TeamCard` gets its score as a prop and doesn't use `useState` anymore. (You'll probably find it easiest to keep one piece of state in `App`: an array of team objects that each have a `score`.)
- The buttons still live inside `TeamCard`, so the card needs a way to tell `App` "add 1 to me." Pass a function down as a prop for that. (This is the callback from the data flow slide.)
- Work out the leader and the total from the scores you already have. Don't give them their own `useState`.

Both buttons from Part 1 should still work when you're done, including the "never below 0" rule.

**Stop.** Which way does data move now, and which way do the clicks go? Where did you put the "never below 0" check, in the card or in `App`, and does it matter? Why shouldn't the leader be its own piece of state? What could go wrong if it was?

## If you finish early

Try one of these:

- Add a **Reset all** button to `App` that puts every score back to 0.
- Add a text box and an **Add team** button that adds a new team with a score of 0. The box should be controlled by state: its `value` comes from state, and typing updates it through `onChange`. Once it works, take the `onChange` off and try typing. What happened, and why?
- **Test something from lecture.** In lecture, the child counter reset every time we clicked the parent's button, and we said it was because the prop changed. Paste the lecture's `ASeperateCounter` inside `App`'s function body, give `App` a counter that it passes down as a prop, and click the parent's button. Then move `ASeperateCounter` *out* of `App` and try again. Did the child still reset? So what was actually causing it?
- Show the scoreboard sorted highest score first, **without** changing the order of the array in your state.
- Highlight the leader's card. What should happen when two teams are tied? What about at the start, when everyone has 0?

## Before you leave

Make sure both names are at the top of `App.jsx`, then commit and push before the end of class:

```bash
git status
git add .
git commit -m "lecture 17 activity"
git push
```

Check the `git status` output first. If `node_modules` shows up in the list, stop and ask before you push.

## Quick reference

```jsx
// state: a value that changes over time, owned by one component
const [score, setScore] = useState(0)
setScore(score + 1)              // set it to a new value

// props: data a parent passes down to a child
<TeamCard name="Quizzly Bears" color="#b5651d" />
function TeamCard({ name, color }) { ... }

// a callback prop: how a child tells its parent something happened
<TeamCard onScore={amount => changeScore(team.id, amount)} />

// JSX
{score}                          // any JavaScript expression goes in { }
className="team-card"            // not class
style={{ color: 'blue' }}        // an object, so two sets of braces
onClick={handleClick}            // pass the function, don't call it
{teams.map(team => <TeamCard key={team.id} ... />)}
```
