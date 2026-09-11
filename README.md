# UK Crime Stats

A small full-stack app: enter a UK address and a month, and it shows how many street-level
crimes of each category the police recorded around that address.

I built it in four days in July 2025 as a take-home assignment. The backend is Flask, the
frontend React with TypeScript.

## How a search works

While you type, the frontend asks OpenStreetMap Nominatim for matching UK addresses. The
request waits for a pause in typing and is cancelled when the input changes. The chosen
address is then turned into a postcode on the client.

The Flask API checks the postcode format, gets its coordinates from the Google Maps
Geocoding API and asks [data.police.uk](https://data.police.uk/docs/) for all street-level
crimes at that point in that month. It returns the number of crimes per category, and the
frontend draws them with Recharts.

A postcode that can't be resolved gets a `400`, a failing upstream API a `502`. If the
police have no data for that month, the page says so instead of drawing an empty chart.

## Stack

- Backend: Python, Flask, `googlemaps`, `requests`
- Frontend: React 19, TypeScript, Vite, TanStack Query, React Hook Form with Zod,
  Tailwind CSS and Recharts, organised by Feature-Sliced Design layers

A Postman collection for the API is in [`backend/postman`](backend/postman).

## Running it locally

You need Python 3, Node.js with pnpm, and a Google Maps API key with the Geocoding API
enabled.

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env            # add your GOOGLE_MAPS_API_KEY
python run.py                   # http://localhost:8000
```

```bash
cd frontend
pnpm install
cp .env.example .env
pnpm run dev                    # http://localhost:5173
```

## What I would do differently

Today I would geocode once, on the server. Resolving the address with Nominatim in the
browser and again with Google on the server means two services and a regular expression
that pulls the postcode out of Nominatim's display name.

I would also stop returning raw exception text from the catch-all error handler, and add
tests. There are none.
