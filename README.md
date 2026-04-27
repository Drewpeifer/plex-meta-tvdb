# TVDB Example Provider

A Plex-compatible Custom Metadata Provider for TV shows using TheTVDB.com API written with the assistance of Claude Code (https://www.claude.com/product/claude-code)

# Archive Notice 04/26

This project has been archived as of April 2026. I no longer consider the TVDB a reliable source of data due to recent
changes in administration and moderation policies. As a result, it has become too difficult to tell when the
problems encountered with the agent are due to local configuration, the agent's logic, or the bad data source. I will leave the
repo as publicly archived, please feel free to fork and use as you see fit.

----------

## Disclaimer

This project is provided as a template for developers for demonstration purposes only. Using this provider for permanent/production use is not recommended.

## Data Attribution

This product uses the TVDB API but is not endorsed or certified by [TheTVDB.com](https://thetvdb.com).

## Setup

### 1. Install Dependencies

```bash
npm install
```

### 2. Configure Environment

Create a `.env` file in the root directory:

```bash
cp .env.example .env
```

Edit `.env` and add your TVDB API key:

```
TVDB_API_KEY=your_api_key_here
PORT=3000
```

Get your API key from: https://thetvdb.com/dashboard/account/apikey

**Optional Configuration:**

```
LOG_LEVEL=info  # Log level: error, warn, info, http, verbose, debug, silly (default: info)
BASE_URL=http://localhost:3000 # Base url for where the server is running
```

### 3. Build

```bash
npm run build
```

### 4. Run

```bash
npm start
```

The server will start on port 3000 (or the PORT specified in .env).

## API Endpoints

### GET /tv
Returns the MediaProvider definition.

**Response:**
```json
{
  "MediaProvider": {
    "identifier": "tv.plex.agents.custom.example.thetvdb.tv",
    "title": "TheTVDB Example TV Provider",
    "version": "1.0.0",
    "Types": [...],
    "Feature": [...]
  }
}
```

### POST /tv/library/metadata/matches
Match feature - searches for TV shows, seasons, or episodes.

**Headers:**
- `X-Plex-Language` (optional): Language code (e.g., "en-US")
- `X-Plex-Country` (optional): Country code (e.g., "US")

**Request Body Examples:**

**Match TV Show:**
```json
{
  "type": 2,
  "title": "Adventure Time",
  "year": 2010
}
```

**Match by External ID:**
```json
{
  "type": 2,
  "guid": "tvdb://152831"
}
```

**Match Season:**
```json
{
  "type": 3,
  "parentTitle": "Adventure Time",
  "index": 1
}
```

**Match Episode:**
```json
{
  "type": 4,
  "grandparentTitle": "Adventure Time",
  "parentIndex": 1,
  "index": 1
}
```

**Manual Search (multiple results):**
```json
{
  "type": 2,
  "title": "Star",
  "manual": 1
}
```

**Response:**
```json
{
  "MediaContainer": {
    "offset": 0,
    "totalSize": 1,
    "identifier": "tv.plex.agents.custom.example.thetvdb.tv",
    "size": 1,
    "Metadata": [
      {
        "type": "show",
        "ratingKey": "tvdb-show-15260",
        "guid": "tv.plex.agents.custom.example.thetvdb.tv://show/tvdb-show-15260",
        "title": "Adventure Time",
        ...
      }
    ]
  }
}
```

## Development

### Run Tests

```bash
npm test
```

Note: Match route integration tests require `TVDB_API_KEY` to be set in environment.

### Watch Mode

```bash
npm run build:watch
```

### Test Coverage

```bash
npm run test:coverage
```

## Project Structure

```
src/
├── config/          # Environment configuration
├── constants.ts     # Shared constants (API paths)
├── models/          # TypeScript interfaces
│   ├── MediaProvider.ts
│   └── Metadata.ts
├── types/           # TVDB API types
│   └── tvdb.ts
├── services/        # Business logic
│   ├── TVDBClient.ts       # TVDB API client with request/response logging
│   ├── MatchService.ts     # Match feature implementation
│   └── MetadataService.ts  # Metadata fetch implementation
├── mappers/         # TVDB to Plex transformations
│   └── TVDBMapper.ts
├── utils/           # Helper functions
│   ├── guid.ts
│   └── logger.ts           # Winston logger with configurable levels
├── providers/       # Provider definitions
│   └── TVProvider.ts
├── routes/          # Express routes
│   └── tvRoutes.ts
├── app.ts           # Express app setup with request logging
└── server.ts        # Server entry point

test/
├── TVProvider.test.ts
├── metadata.test.ts
├── guid.test.ts
├── TVDBMapper.test.ts
├── tvRoutes.test.ts
└── matchRoutes.test.ts

docs/
├── MediaProvider.md
├── Metadata.md
└── API Endpoints.md
```

## Supported Features

- ✅ TV Show matching by title and year
- ✅ TV Show matching by external ID (IMDB, TVDB, TMDB)
- ✅ Season matching by show title and season number
- ✅ Episode matching by show title, season number, and episode number
- ✅ Episode matching by air date
- ✅ Manual search mode (multiple results)
- ✅ Localization support (X-Plex-Language header)
- ✅ Content ratings by country (X-Plex-Country header)
- ✅ Complete metadata mapping (images, genres, cast, crew, ratings, networks, etc.)
- ✅ Alternative season orderings (DVD, absolute, etc.)

## License

ISC
