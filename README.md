# empowHER

empowHER is a team-built clinical-workflow prototype created for GenAI Genesis 2026, where it was named an IBM track runner-up. It explores a clinician-facing workspace for turning a visit transcript into a structured handoff, a draft care-plan view, symptom-trend context, and referral-coordination artifacts.

The project addresses a workflow problem rather than a diagnostic one: information from a visit is often spread across notes, follow-up tasks, symptom observations, and referral requirements. empowHER prototypes how an AI-assisted interface could help organize that context for clinician review. It is a hackathon prototype, not a production healthcare product.

## What is implemented

- **Visit Scribe** accepts a pasted visit transcript, calls a backend workflow endpoint, and displays an AI-generated handoff with an energy level and symptom tags.
- **Care Plan Workspace** uses the saved handoff to request a draft explanation, next step, and benefit guidance. The UI presents these as reviewable content and includes clinician-review prompts.
- **Operations Dashboard** renders generated visit, care-plan, alert, and KPI payloads from the current workflow context.
- **Symptom Tracker** shows generated symptom-log entries and trend data, including a client-rendered severity chart and a local review form.
- **Referrals & Benefits** displays a generated referral queue, packet checklist, benefits summary, and coordination blockers.
- **Workflow state** is retained in the browser with `localStorage`, allowing the handoff and care-plan draft to move between pages during a local session.
- **Usable UI states** are implemented across the workflow: loading, empty, error, and AI-output states are explicitly handled rather than represented by static mock screens alone.

These flows produce draft workflow artifacts. They do not make diagnoses, submit referrals, verify insurance, or connect to an electronic health-record system.

## Architecture

The actively developed interface lives in [`frontend/`](frontend/) as a Next.js App Router application. Its page-level workflow is:

```text
Next.js + React UI
  -> browser localStorage handoff state
  -> Express API on localhost:5050
  -> IBM watsonx.ai chat endpoint
  -> structured response rendered for review
```

The frontend calls the Express service through `NEXT_PUBLIC_API_BASE_URL`, which defaults to `http://localhost:5050`. The service exposes workflow routes for visit-scribe, dashboard, symptom-tracker, and referrals/benefits payloads, plus supporting AI and scan routes. The watsonx.ai service wrapper reads server-side environment variables, sends prompts to the configured IBM model, and validates/parses structured model output before returning it to the frontend.

The repository also contains an earlier root-level Vite/React setup used to run the Express service, plus Python modules under [`ai/`](ai/) for watsonx.ai-backed extraction, care-plan, referral-packet, benefits, and symptom-summary experiments. A small FastAPI app under [`backend/`](backend/) is present as a placeholder API and is not the application path used by the Next.js frontend.

## Technology stack

- **Frontend:** Next.js, React, TypeScript, Tailwind CSS, Carbon React, Carbon Icons, and Recharts
- **API service:** Node.js, Express, TypeScript, CORS, and Multer for the image-upload route
- **AI integration:** IBM watsonx.ai through a server-side service wrapper
- **Prototype utilities:** Python, FastAPI, and IBM watsonx machine-learning client dependencies for the experimental AI layer
- **Tooling:** ESLint, Vite, tsx, and npm

`shadcn` configuration and Tailwind utility helpers are included in the frontend; the project’s visible component system is primarily Carbon React with a small local button wrapper.

## My contribution

This was a team hackathon project. My role focused on frontend engineering and product UX leadership: shaping the clinician-workflow experience, translating the workflow into page and component structure, and driving the interaction patterns for transcript handoff, draft review, empty/loading/error states, and navigation between workflow stages.

The repository contains contributions from the team across the frontend, API, and AI experimentation layers. This README does not attribute backend or AI implementation solely to me.

## Local setup

The current local workflow uses two processes: the Express API from the repository root and the Next.js frontend from `frontend/`.

1. Install the root dependencies and create a local `.env` file with your own watsonx.ai configuration:

   ```bash
   npm install
   ```

   Required server variables:

   ```dotenv
   WATSONX_API_KEY=
   WATSONX_PROJECT_ID=
   ```

   Optional variables supported by the service include `PORT`, `CLIENT_ORIGIN`, `WATSONX_URL`, and `WATSONX_MODEL_ID`.

2. Start the Express API on port `5050`:

   ```bash
   npm run server
   ```

3. In a second terminal, install and run the Next.js frontend:

   ```bash
   cd frontend
   npm install
   npm run dev
   ```

4. Open `http://localhost:3000`. To use a non-default API address, set `NEXT_PUBLIC_API_BASE_URL` before starting the frontend.

The root `npm run dev` command starts the older Vite setup on port `5173`; it is not the Next.js interface described above. No deployment configuration, working demo URL, product screenshots, or demo video is included in this repository.

## Testing and limitations

There is no configured npm test command or CI workflow in the repository. [`test_ai.py`](test_ai.py) is a manual Python script that exercises parts of the watsonx.ai prototype and requires local credentials; it is not an isolated automated test suite.

empowHER is intended only for synthetic or demo inputs. Do not enter, process, or store real patient data with this repository. The code does not establish clinical validation, privacy compliance, access controls, audit logging, EHR integration, or production data handling. AI-generated content must be reviewed by a qualified clinician and should not be used as medical advice or as the basis for patient-care decisions.

## License

No license file is currently included in this repository.
