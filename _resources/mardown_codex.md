---
layout: page
title: Markdown + Codex
---

# {{page.title}}

**Goal:** Use Codex and Markdown to create a simple, single-page website.

## Requirements

Install VS Code with the following extensions:

- Markdown All in One
- Markdown PDF
- OpenAI Codex

You will also need a free ChatGPT account.

## Step 1: Set Up Your Workspace

1. In your Documents folder, create a folder named `potato-site`.
2. Download the ZIP file from Blackboard and extract its contents into the `potato-site` folder.
3. Make sure the `potato-site` folder contains only the following files:

   - `potato_website_summary.md`
   - `potato_nutrition_dataset.json`
   - `potato1.jpg`
   - `potato2.jpg`
   - `potato3.jpg`
   - `potato4.jpg`
   - `potato5.jpg`

## Step 2: Set Up Codex

1. Use the disposable email address we created in previous activities to create a ChatGPT account.
2. Install the official OpenAI Codex extension in VS Code.
3. Sign in to Codex using your new ChatGPT account.

## Step 3: Create the HTML, CSS, and JavaScript Files

1. Convert `potato_website_summary.md` to HTML and save the result as `index.html`.
2. Create a new file named `styles.css`.
3. Create a new file named `app.js`.

## Step 4: Generate CSS for the Website

1. Use Codex to generate modern CSS styles for the HTML created in the previous step.
2. Enter the following prompt:

```markdown
# Task

Use the `index.html` file in the current directory to create the contents of `styles.css` in the same directory.

# Requirements

- Include responsive CSS media queries for mobile devices.
- Use a modern design with rounded corners.
- Use Google Fonts: Fraunces for headings and Sora for body text.
- Use the following color scheme:

/* CSS HEX */
--olive-leaf: #556B12ff;
--saddle-brown: #8C4C1Aff;
--golden-bronze: #C59823ff;
--jasmine: #F1D586ff;
--rich-mahogany: #311207ff;
```

## Step 5: Add JavaScript Functionality

1. Use Codex to add basic JavaScript functionality to the website.
2. Enter the following prompt:

```markdown
# Background

I am building an educational website about potatoes using HTML, CSS, and vanilla JavaScript.

# Task

Implement the following features:

- Add a toggle that switches between light and dark modes.
- Add a nutrition calculator section where users enter the number of grams of potato they plan to eat. The page should calculate estimated calories, carbohydrates, protein, and other nutrients using the supplied nutrition dataset(`potato_nutrition_dataset.json`).

# Requirements

- Use plain JavaScript without frameworks or external libraries.
- Add the JavaScript code to `app.js`.
- Keep the HTML, CSS, and JavaScript in separate files. The files are in the current directory:
  - HTML: `index.html`
  - CSS: `styles.css`
  - JavaScript: `app.js`
- Make the features responsive and accessible, using semantic HTML and keyboard-friendly controls.
- Validate user input and handle empty or invalid values.
- Do not modify unrelated parts of the website.
- Add comments explaining important sections of the JavaScript.
- Explain how the code works in beginner-friendly language.
- Provide instructions for testing the features in a web browser.
```

## Step 6: Preview Your Work

1. Install the **Live Preview** extension in VS Code.
2. Open `index.html`.
3. Right-click an empty area of the editor and select **Open with Live Preview**.
4. Test the website, including the light and dark mode toggle and the nutrition calculator.
