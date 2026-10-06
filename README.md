# CrewAI Workshop: Interview Preparation Crew

Build a team of AI agents that helps you prepare for a job interview. This hands-on Python notebook combines company research, optional interviewer research, tailored interview questions, and an interactive mock interview using **CrewAI**.

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ChrRepou/CrewAI-workshop/blob/main/Interview-Preparation-Crew.ipynb)

## What you will learn

- Define agents with roles, goals, backstories, and tools.
- Create tasks and pass research context between them.
- Coordinate agents with a sequential Crew.
- Use Pydantic models for structured question output.
- Run crews asynchronously inside a notebook.
- Manage questions, answers, feedback, and session history with a typed CrewAI Flow.

## What the notebook does

1. Collects the company, job position, job description, and optional interviewer name.
2. Researches the company using web search and website scraping.
3. Researches the interviewer's public professional background when a name is provided.
4. Requests 20 interview questions tailored to the role and research.
5. Runs a mock interview using the **first 3 generated questions** for the workshop demo.
6. Gives feedback after each answer and a final assessment of the practice session.

### Agents and tools

| Component | Purpose |
| --- | --- |
| Technical Interview Researcher | Researches the company and, optionally, the interviewer. |
| Interview Coach | Generates questions, evaluates answers, and summarizes performance. |
| `SerperDevTool` | Searches the web through the Serper API. |
| `ScrapeWebsiteTool` | Reads website content for research. |
| Gemini LLM | Powers both agents through CrewAI's `LLM` interface. |

The Preparation Crew executes research and question generation sequentially. The `InterviewPrepFlow` then collects your answers, runs a Feedback Crew for each question, stores the session history, and runs a Summary Crew at the end.

## Getting started with Google Colab

The notebook is written for **Google Colab** and uses Colab Secrets to load API keys.

### 1. Open the notebook

Click **Open in Colab** above. Save a copy to your Google Drive if you want to keep your changes.

### 2. Add your API keys

You will need:

| Colab secret | Used for | Get a key |
| --- | --- | --- |
| `GEMINI_API_KEY` | Gemini model requests | [Google AI Studio](https://aistudio.google.com/apikey) |
| `SERPER_API_KEY` | Web search | [Serper](https://serper.dev/) |

In Colab, open **Secrets** using the key icon in the left sidebar. Add both secrets with the exact names above and enable **Notebook access** for each one.

The notebook reads them with `google.colab.userdata` and sets the corresponding environment variables. Keep API keys in Secrets rather than notebook cells.

### 3. Install dependencies and configure the model

Run the notebook cells from top to bottom. The first cell installs CrewAI and its tools:

```python
!pip install 'crewai[tools]' -q
```

The LLM cell currently contains:

```python
llm = LLM(
    model="gemini/gemini-3.5-flash-lite",
    temperature=0.5
)
```

Before running the agents, check that this model is available to your API account. If it is unavailable, replace the model string with an accessible Gemini model supported by your installed CrewAI version.

### 4. Enter your interview details

When prompted, provide:

- **Interviewer:** optional; leave blank to skip interviewer research.
- **Company:** the organization you are interviewing with.
- **Job position:** the role you are applying for.
- **Job description:** paste the relevant responsibilities and requirements.

Run the remaining cells in order. The research and question-generation cells display their results before the mock interview starts.

### 5. Practice your answers

Type an answer to each question when prompted. Empty answers are requested again.

After each answer, the coach provides:

- A score from 1 to 10 with a brief justification.
- What was good.
- What needs improvement.
- Specific actions to strengthen the answer.

After the demo questions, the final assessment summarizes recurring strengths and weaknesses, gives three preparation priorities, and assigns a readiness score.

## Generated questions

The question-generation task requests this distribution:

| Category | Requested questions |
| --- | ---: |
| Culture and Team Fit | 5 |
| Job Position Fit | 7 |
| Background and Ways of Working | 5 |
| Growth Mindset | 3 |
| **Total** | **20** |

Questions are parsed into `InterviewQuestionList`, with a category and question text for each item. The category counts are prompt instructions; the Pydantic schema does not enforce them.

## Customize the workshop

### Practice more questions

Inside `InterviewPrepFlow.run_interview`, change:

```python
questions = self.state.interview_questions[:3]
```

For example, use `[:5]` for five questions, or practice all generated questions:

```python
questions = self.state.interview_questions
```

### Adapt the agents and feedback

Edit the agents' roles, goals, and backstories to change their focus. Adjust the task descriptions and expected outputs to change research priorities, question categories, or feedback criteria.

### Run outside Colab

For local Jupyter use, clone the repository:

```bash
git clone https://github.com/ChrRepou/CrewAI-workshop.git
cd CrewAI-workshop
```

Use a Python environment compatible with CrewAI and install Jupyter and the notebook dependencies:

```bash
python -m pip install jupyterlab 'crewai[tools]'
python -m jupyter lab
```

Open `Interview-Preparation-Crew.ipynb`. Replace the Colab-specific secret-loading cell with environment-variable access, and set both API keys in your local environment before starting Jupyter:

```python
gemini_api_key = os.environ["GEMINI_API_KEY"]
serper_api_key = os.environ["SERPER_API_KEY"]
```

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Missing secret or secret-access error | Confirm both secret names and enable Notebook access in Colab. |
| Model not found or access denied | Check the configured Gemini model and your account's access. |
| API quota or rate-limit error | Check the relevant provider's quota and billing, then retry when available. |
| Missing variable or no interview questions | Run the cells in order and confirm the Preparation Crew completed successfully. |
| Structured question output is unavailable | Inspect the question-generation output; rerun or adjust the task/model if parsing fails. |
| `google.colab` import error locally | Replace the Colab secret-loading cell as described above. |

## Notes

- Model calls and web searches use external APIs and may incur charges.
- Review research against its sources. Generated questions and scores are practice aids, and may vary between runs.
- Interviewer research is prompted to use public professional information, verify identity, and avoid personal speculation.
- Inputs and answers are sent to the configured services as needed; avoid entering confidential information.
- Session history and the final summary are stored in Flow state and displayed in the notebook. The notebook does not implement file export.
- The dependency installation is unpinned, so behavior may change between package versions.

## Resources

- [CrewAI documentation](https://docs.crewai.com/)
- [Google AI Studio](https://aistudio.google.com/)
- [Serper](https://serper.dev/)
- [Google Colab](https://colab.research.google.com/)

Created by [Christina Repou](https://github.com/ChrRepou).
