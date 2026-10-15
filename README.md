# UBA Questionary Frontend

> No momento o deploy desse projeto é inviável por questões financeiras e de usabilidade, o projeto funciona porém foi feito para estudantes de medicina da UBA (Universidad de Buenos Aires) e o uso não teria aplicabilidade real o suficiente para eu arcar com os custos então criei um projeto inspirado nesse porém para o público do ENEM ([https://github.com/pedro-vlps/enem_questionary_front](https://github.com/pedro-vlps/enem_questionary_front))

UBA Questionary is a study platform developed by [Pedro Vieira](https://github.com/pedro-vlps) for medical students at the Universidad de Buenos Aires. Students generate anatomy and biology questions, answer them with explanations, and revisit their answer history through a Spanish-language interface.

This repository contains the React application. The [Python API](https://github.com/pedro-vlps/uba_questionary_api) handles AI generation, persistence, authorization, and Stripe integration.

## Engineering at a Glance

| Area | Implementation |
| --- | --- |
| Interface | React 19, JavaScript, Bootstrap 5, React Bootstrap |
| Navigation | React Router 7 with authentication and access-dependent redirects |
| State | React Context and hooks with local storage persistence |
| API integration | Axios with centralized token and institution headers |
| Verification | Jest and React Testing Library through React Scripts |
| Serving | Express 5, runtime API configuration, Docker image |

## Student Experience

1. Register or sign in, with password and nickname recovery when needed.
2. Access the study area and start a Stripe checkout when a question package is required.
3. Select anatomy or biology and choose a topic for question generation.
4. Submit an answer, then see the correct alternative and explanations.
5. Review answered questions, update the profile, or send feedback.

The study routes account for authentication, institution selection, subscription access, and remaining question allowance. The backend remains responsible for enforcing those permissions.

## Implementation Highlights

- **Connected product flows:** account management, checkout, study sessions, answer history, and feedback share the same application.
- **Consistent API requests:** a shared Axios helper attaches JWT and institution headers and clears the session after protected requests return HTTP 401.
- **Access-dependent navigation:** route guards guide students based on their session and package state.
- **Answer submission feedback:** saving and error states prevent duplicate clicks and keep explanations hidden until the answer is saved.
- **Runtime configuration:** the Express server exposes the API URL at runtime, allowing the built application to connect to a configured backend.
- **Behavior-oriented tests:** suites exercise pages, navigation, context state, API requests, and shared interface elements.

## Code Review Guide

| Start here | What to inspect |
| --- | --- |
| [Application routes](src/App.jsx) | Navigation and access-dependent redirects |
| [Application context](src/helpers/ContextApi.jsx) | Session, institution, question state, and usage |
| [API helper](src/helpers/FecthApi.jsx) | Headers, API URL resolution, and unauthorized responses |
| [Question page](src/pages/Question.jsx) | Answer submission and explanations |
| [Study selection](src/pages/SubjectSelection.jsx) | Study entry point and checkout |
| [Question tests](src/tests/pages/Question.test.jsx) | Observable answer behavior |
| [API helper tests](src/tests/helpers/FecthApi.test.jsx) | Request and session behavior |

## Project Structure

```text
src/
  Elements/       # shared header and footer
  helpers/        # context, API client, translations, and input validation
  Images/         # application branding
  pages/          # account, study, history, profile, and feedback screens
  tests/          # page, helper, navigation, and component tests
  App.jsx         # application composition and routes
  index.js        # React entry point
public/           # HTML shell and static assets
server.js         # static serving and runtime configuration
```

## Run Locally

Use Node.js and npm; the supplied Docker image uses Node.js 20.

```bash
npm ci
```

Create a root `.env.local`:

```env
REACT_APP_BASE_API_URL=http://localhost:8000
```

Start the [backend](https://github.com/pedro-vlps/uba_questionary_api) following its README, then run:

```bash
npm start
```

Open `http://localhost:3000`. The API must allow that origin through its CORS configuration. Restart the development server after changing environment variables.

Registration and study flows require the API and initialized database. Question generation requires backend OpenAI credentials; checkout requires backend Stripe configuration and the corresponding access records.

## Tests and Build

```bash
npm test -- --watchAll=false
npm run test:coverage
npm run build
```

Tests use mocked API interactions for frontend behavior; they do not verify live payments or AI responses.

To serve the production build locally:

```bash
node server.js
```

The Express server uses port `8080` by default, supports client-side route fallback, and serves `/env-config.js` with `REACT_APP_BASE_API_URL` from its process environment. Runtime configuration takes precedence over the build-time API URL.

A container build is available through [Dockerfile.railway](Dockerfile.railway).

## Current Scope

No public deployment is maintained. The repository can be evaluated through its source, tests, and local setup. The current interface is configured for Spanish (`es-AR`).

The related [ENEM project](https://github.com/pedro-vlps/enem_questionary_front) adapts the study-platform idea for a broader Brazilian exam-preparation audience.
