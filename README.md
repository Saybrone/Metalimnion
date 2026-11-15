# Metalimnion

Metalimnion is a visual analysis app which simplifies the viewing and observation of lakes worldwide and significant changes that have affected said lakes. Metalimnion is a sophisticated app that can display individual measurements of area and surface temperature over the years and analyze these measurements to attribute changes to climatological phenomena or human activity. The analyses are then evaluated on the basis of reliability and are summarized. All analyzed lakes are shown on the various available easy-to-navigate maps with helpful circles and name descriptors. It addresses the challenge by making significant changes regarding lakes much easier to view and reason about, especially for non-experts. It allows everyone to see the change and future predictions concerning water area and surface temperature allowing them to act against the day by day shrinking of water resources, which are already very limited. This matters because global water trends affect many living things worldwide, and more attention must be drawn to crucial concerns such as this if we, the people, want to tend to our planet well.

Team: Augmented 6th, NASA Space Apps Challenge 2026.

## Features

- World lake map: click a lake to see its water area since 1985 (Landsat) and surface temperature, with trend, reliability and a plain-language explanation.
- Climate versus human cause: precipitation and evaporation (NASA POWER / MERRA-2) compared with the area change.
- Compare lakes side by side; look up other lakes and run an on-demand analysis (needs internet access to the data services).
- Selectable basemaps (NASA GIBS satellite imagery, OpenFreeMap vector maps), English and Turkish interface, About page with glossary and data sources.

## Deploy on Render

1. Push this repository to GitHub.
2. In Render choose New -> Blueprint and pick the repository. `render.yaml` defines one Docker web service named `metalimnion` on the free plan (region Frankfurt, auto-deploy on commit to `main`).
3. The health check path is `/api/health`. No secrets and no disk are needed; files written at runtime are ephemeral.

The Dockerfile builds the React frontend (Node 22) and serves it together with the FastAPI backend (Python 3.12) from one image on one port. Render sets `PORT`.

### Environment variables (read by the code)

| Variable | Meaning |
|---|---|
| `PORT` | Port uvicorn listens on (Render injects it; default 8000). |
| `LLD_BASIN_FALLBACK` | `1` skips the 360 MB HydroBASINS download and uses a buffer around the lake as the basin (set in `render.yaml` for 512 MB hosts). |
| `LLD_OFFLINE` | `1` makes the app behave like a machine without internet (no live data, no on-demand analysis from outside sources). Optional. |
| `LLD_LIVE_TTL_S` | Cache lifetime in seconds of the live cards (default 10800 = 3 h). Optional. |

## Run locally with Docker

```
docker build -t metalimnion .
docker run --rm -p 8000:8000 metalimnion
```

Then open http://localhost:8000. (Not verified here: the Docker image build itself was not run for this commit; the frontend build and the backend were checked outside Docker.)

## Repository layout

- `Dockerfile`, `render.yaml`: build and deploy definition.
- `backend/`: FastAPI app (`backend/api`), analysis code (`backend/core`, `backend/jobs`).
- `frontend/`: React + Vite + MapLibre + ECharts source.
- `data/`: precomputed lakes (area series, masks, climate), lake index, offline basemap and demo snapshots served by the app.
- `requirements.txt`: Python runtime dependencies.

## Data sources

NASA

1. [NASA POWER monthly API](https://power.larc.nasa.gov/)
2. [MERRA-2 tavg1_2d_lnd_Nx (NASA GMAO)](https://www.earthdata.nasa.gov/data/catalog/ges-disc-m2t1nxlnd-5.12.4)
3. [NASA GIBS, layer VIIRS NOAA-20 Corrected Reflectance True Color](https://www.earthdata.nasa.gov/engage/open-data-services-software/earthdata-developer-portal/gibs-api)
4. [NASA GIBS, layer Blue Marble Shaded Relief and Bathymetry](https://www.earthdata.nasa.gov/engage/open-data-services-software/earthdata-developer-portal/gibs-api)

NASA Partnered

5. [Landsat 4-5 TM Collection 2 Level-2 (NASA/USGS)](https://www.usgs.gov/centers/eros/science/usgs-eros-archive-landsat-archives-landsat-4-5-tm-collection-2-level-2-science)
6. [Landsat 8-9 OLI/TIRS Collection 2 Level-2 (NASA/USGS)](https://www.usgs.gov/centers/eros/science/usgs-eros-archive-landsat-archives-landsat-8-9-olitirs-collection-2-level-2)

Non-NASA

7. [Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/dataset/landsat-c2-l2)
8. [HydroBASINS (HydroSHEDS)](https://www.hydrosheds.org/products/hydrobasins)
9. [Natural Earth 10m lakes and countries](https://www.naturalearthdata.com/downloads/10m-physical-vectors/)
10. [OpenFreeMap](https://openfreemap.org/)

## License

MIT, see `LICENSE`. Copyright (c) 2026 Augmented 6th.
