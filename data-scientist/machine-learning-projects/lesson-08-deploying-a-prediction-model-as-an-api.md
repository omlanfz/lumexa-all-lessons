# Course 15 · Lesson 8: Deploying a Prediction Model as an API

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain what an API is and why a trained model needs to be "served" to be
  useful to other applications
- Save a trained scikit-learn pipeline with `joblib` and reload it in a
  separate process
- Build a real Flask API with a POST endpoint that validates input and
  returns a real prediction
- Send a real request to a running API and interpret the JSON response

## What You'll Learn
- Why `joblib` (not just re-running training) is how models move from
  training to production
- HTTP basics needed to understand a prediction API (POST requests, JSON
  bodies, status codes)
- Building a Flask app with `/predict` and `/health` endpoints
- Input validation so the API fails safely on bad requests instead of
  crashing

## Why It Matters
A model sitting in a Jupyter notebook helps nobody but you. A real product
— a website estimating your house's value, an app predicting if you'll
enjoy a movie — needs the model running as a live service that other code
can call at any time, instantly, without retraining. This lesson turns your
trained model into exactly that kind of real, callable service.

## Real-World Connection
When a real-estate website shows you an "estimated value," or a streaming
app shows "recommended for you," there's very likely a trained ML model
sitting behind an API almost identical in structure to the one you'll build
in this lesson — the app sends the model some data over the internet, and
gets a real prediction back in milliseconds.

## Key Terminology
- **API (Application Programming Interface)**: a defined way for one piece
  of software to ask another piece of software to do something and get a
  response — here, "predict a value from this data."
- **Endpoint**: a specific URL path an API exposes (e.g. `/predict`).
- **HTTP method**: the "verb" of a request; we use `POST` because we're
  sending data to be processed, not just asking to view something.
- **JSON**: a text format for structured data (`{"key": "value"}`) used to
  send and receive data over the web.
- **Serialization**: converting a trained model object into a file that can
  be saved and reloaded later (we use `joblib` for this).
- **Status code**: a number in the HTTP response indicating success (200)
  or a kind of failure (e.g. 400 for a bad request).

## Concept Explanation

Every model you've trained in this course lived only inside the Python
process that trained it — the moment that script finished running, the
trained model vanished from memory. That's fine for learning, but useless
for a real application: you can't ask a website's model to make a
prediction if the model only exists for a few seconds during training.

The first piece of the solution is **serialization** — saving the fully
trained model object to a file on disk so it can be loaded again later,
by a completely different program, without retraining. `joblib` is the
standard tool for this in the scikit-learn world (it's particularly
efficient with the large NumPy arrays inside trained models). You've
already been doing this throughout the course: every project's `train.py`
ends with `joblib.dump(...)`, saving the model to `models/model.joblib`.

The second piece is **serving** — running a small web server whose entire
job is to (1) load that saved model once when it starts, (2) listen for
incoming requests containing new data, (3) run that data through the
model's `.predict()`, and (4) send the prediction back as a response. This
is exactly what a **Flask** app does. We define an **endpoint** — a URL
path like `/predict` — and tell Flask what Python function to run whenever
a request arrives at that path. We use the `POST` HTTP method because the
caller is sending data (the house's features, say) for the server to
process, not simply asking to view a page.

The data sent to and returned from the API is formatted as **JSON**, a
simple, universal text format that looks almost exactly like a Python
dictionary: `{"median_income": 5.2, "ocean_proximity": "NEAR BAY", ...}`.
Flask parses the incoming JSON into a Python dictionary automatically, and
we build a response dictionary that Flask converts back into JSON for the
reply.

Critically, a real production API can't simply trust that every request
will contain exactly the right fields in the right format — real callers
make real mistakes (a missing field, a typo in a category name, a string
where a number was expected). **Input validation** checks the incoming
data *before* handing it to the model, and returns a clear error with an
appropriate status code (like `400 Bad Request`) when something's wrong,
instead of letting the server crash or silently return a meaningless
prediction. This is what separates a classroom toy from something you'd
actually trust in a real product.

## Step-by-Step Instruction

1. Confirm you have a saved `model.joblib` from an earlier project (we'll
   use Project 07's house-price model as the running example).
2. Write a Flask app that loads the model once, at startup (not on every
   request — that would be slow).
3. Add a `/health` endpoint to quickly check the API is alive and which
   model it loaded.
4. Add a `/predict` endpoint: validate the incoming JSON, build a DataFrame
   matching what the model expects, call `.predict()`, and return the
   result as JSON.
5. Run the API locally and send it a real test request with `curl` or
   Python's `requests` library.

## Code

```python
"""
Lesson 8 demo: a real Flask API serving Project 07's saved house-price
model. This mirrors projects/07-house-price-predictor/api.py.

Run this file, then in a separate terminal send a real request:
    curl -X POST http://127.0.0.1:5000/predict \
      -H "Content-Type: application/json" \
      -d '{"longitude": -122.25, "latitude": 37.85, "housing_median_age": 30.0,
           "total_rooms": 2500.0, "total_bedrooms": 450.0, "population": 900.0,
           "households": 420.0, "median_income": 5.2, "ocean_proximity": "NEAR BAY"}'
"""
import joblib
import pandas as pd
from flask import Flask, jsonify, request

MODEL_PATH = "models/model.joblib"   # path to a joblib file saved by train.py

app = Flask(__name__)

# Load the model ONCE, when the server starts -- not on every request.
_bundle = joblib.load(MODEL_PATH)
MODEL = _bundle["model"]

REQUIRED_FIELDS = [
    "longitude", "latitude", "housing_median_age", "total_rooms",
    "total_bedrooms", "population", "households", "median_income",
    "ocean_proximity",
]
VALID_OCEAN_PROXIMITY = {"<1H OCEAN", "INLAND", "NEAR OCEAN", "NEAR BAY", "ISLAND"}


@app.route("/health", methods=["GET"])
def health():
    """A simple endpoint to confirm the API is running and which model loaded."""
    return jsonify({"status": "ok", "model": _bundle.get("best_model_name")})


@app.route("/predict", methods=["POST"])
def predict():
    payload = request.get_json(silent=True)
    if payload is None:
        return jsonify({"error": "Request body must be valid JSON"}), 400

    missing = [f for f in REQUIRED_FIELDS if f not in payload]
    if missing:
        return jsonify({"error": f"Missing required fields: {missing}"}), 400

    if payload["ocean_proximity"] not in VALID_OCEAN_PROXIMITY:
        return jsonify({
            "error": f"ocean_proximity must be one of {sorted(VALID_OCEAN_PROXIMITY)}"
        }), 400

    try:
        row = {f: payload[f] for f in REQUIRED_FIELDS}
        df = pd.DataFrame([row])
        # Recreate the exact same engineered features used at training time
        df["rooms_per_household"] = df["total_rooms"] / df["households"]
        df["bedrooms_per_room"] = df["total_bedrooms"] / df["total_rooms"]
        df["population_per_household"] = df["population"] / df["households"]

        prediction = float(MODEL.predict(df)[0])
    except Exception as exc:  # noqa: BLE001
        return jsonify({"error": f"Prediction failed: {exc}"}), 400

    return jsonify({
        "predicted_median_house_value": round(prediction, 2),
        "model": _bundle.get("best_model_name"),
    })


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

## Code Explanation
- `joblib.load(MODEL_PATH)` runs exactly once, when the file is first
  executed — the model stays loaded in memory ready to serve many requests
  quickly, instead of reloading from disk on every single call.
- `/health` (`GET`) is a common real-world pattern: a lightweight endpoint
  monitoring tools can call every few seconds to confirm the service is
  alive.
- `/predict` (`POST`) first checks the request actually contains valid JSON,
  then checks every required field is present, then checks
  `ocean_proximity` is one of the categories the model was actually trained
  on — three separate, deliberate validation checks before ever touching
  the model.
- We recompute the same engineered features (`rooms_per_household`, etc.)
  inside the API exactly as they were computed during training — this
  consistency between training-time and serving-time feature engineering is
  essential; a mismatch here is one of the most common real bugs in
  deployed ML systems.
- Wrapping the prediction call in `try/except` ensures an unexpected error
  returns a clean 400 response instead of crashing the whole server.

## Expected Output
When you run this file and send the example `curl` request from the
docstring, you get back real JSON like:
```json
{"model":"random_forest_tuned","predicted_median_house_value":335830.37}
```
`GET /health` returns:
```json
{"model":"random_forest_tuned","status":"ok"}
```
Sending a request missing required fields correctly returns HTTP 400 with a
descriptive error listing exactly which fields were missing.

## Guided Practice
1. Send a request with `"ocean_proximity": "SPACE"` (an invalid category).
   Confirm you get a 400 error naming the valid options.
2. Send a request missing `"median_income"` entirely. Confirm the error
   message correctly lists it as missing.
3. Call `/health` before and after intentionally breaking the model path
   (e.g. temporarily renaming `model.joblib`) and discuss what happens
   (the app fails to even start, since loading happens at startup — this
   is a feature, not a bug: better to fail loudly at startup than silently
   serve broken predictions).

## Hands-On Activity
In pairs, have one student send requests (using `curl` or Python
`requests`) while the other reads the Flask terminal logs and explains, in
plain language, what happened for each request (success, missing field,
invalid category).

## Student Challenge
Add a new endpoint, `/predict/batch`, that accepts a JSON list of multiple
houses at once (`{"houses": [...]}`) and returns a list of predictions —
requiring you to validate every item in the list, not just one.

## Common Mistakes
- Loading the model *inside* the `/predict` function instead of once at
  startup — this works but is much slower per-request in real use.
- Forgetting to recompute engineered features inside the API exactly as
  they were computed during training — leads to silently wrong predictions.
- Not validating input at all, letting a malformed request crash the
  server with an unhandled exception.
- Testing only the "happy path" and never sending a deliberately broken
  request to confirm error handling actually works.

## Debugging Guidance
- `Address already in use` — another process (maybe a previous run of this
  same API) is still using port 5000; stop it or use a different port.
- `FileNotFoundError` for `model.joblib` — confirm you ran the project's
  `train.py` first and are running the API from the correct working
  directory.
- If predictions look wrong, print the DataFrame right before
  `MODEL.predict(df)` and confirm every column matches what training used,
  in the right types.

## Mini Quiz
1. Why do we load the model once at startup instead of on every request?
2. What HTTP method do we use for `/predict`, and why?
3. Name two things this lesson's API validates before making a prediction.
4. What happens if `TotalCharges` (or any expected field) is missing from
   the request body?

### Answer Key
1. Loading is comparatively slow; doing it once keeps every subsequent
   request fast.
2. `POST`, because the caller is sending data to be processed, not just
   requesting to view something.
3. That the request body is valid JSON, that all required fields are
   present, and that `ocean_proximity` is one of the categories the model
   was trained on (any two of these).
4. The API returns a `400` error response listing exactly which required
   fields were missing, instead of crashing or guessing.

## Lesson Recap
A trained model only becomes useful to other software once it's saved with
`joblib` and served through a real API. Flask lets you define endpoints
(`/health`, `/predict`) that load the model once, validate incoming JSON
requests, and return real predictions as JSON — turning a script into a
genuine, callable service.

## Homework / Extension
Write a short Python script using the `requests` library (instead of
`curl`) that sends 3 different real test payloads to a running API
(one valid, one missing a field, one with an invalid category) and prints
each response's status code and body.

## Portfolio Project Connection
This lesson's Flask pattern is exactly `api.py` in **Project 07 (House
Price Predictor)** — already built, run, and tested end-to-end with a real
request returning `{"predicted_median_house_value": 335830.37}`. The same
validate-then-predict pattern could be applied to Project 08's churn model
as an extension.
