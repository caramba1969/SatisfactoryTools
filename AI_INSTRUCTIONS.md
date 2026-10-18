# Satisfactory Tools - AI Instructions

This document provides a high-level overview of the **Satisfactory Tools** project architecture, conventions, and development workflow to assist AI agents and developers in understanding and modifying the codebase.

## Project Overview

**Satisfactory Tools** is a web application for the game *Satisfactory*. It provides tools for production planning, a codex for items/buildings/schematics, and visualization of production lines.

*   **Framework**: AngularJS 1.x (Legacy Angular)
*   **Language**: TypeScript
*   **Build System**: Webpack 4
*   **Styling**: SCSS (Bootstrap 4)
*   **Data Source**: Static JSON files generated from game data.

## Directory Structure

*   **`bin/`**: Node.js scripts for parsing game data and generating JSON files (e.g., `parseDocs.ts`).
*   **`data/`**: Contains the generated JSON data files (`data.json`, `data1.0.json`).
*   **`src/`**: Main application source code.
    *   **`app.ts`**: Application entry point.
    *   **`Constants.ts`**: Global constants.
    *   **`Data/`**: Data access layer.
        *   `DataProvider.ts`: Manages switching between game versions (e.g., '0.8', '1.0').
        *   `Data.ts`: Main accessor class for querying items, recipes, buildings.
    *   **`Module/`**: AngularJS application modules.
        *   `AppModule.ts`: Main module definition and Routing configuration (`ui-router`).
        *   `Components/`: Reusable UI components (`.ts` logic).
        *   `Controllers/`: Page-level controllers.
        *   `Directives/`: Custom directives.
        *   `Services/`: Business logic services (e.g., `ProductionController`, `ItemFiltersService`).
    *   **`Schema/`**: TypeScript interfaces defining the shape of the data (Items, Recipes, Buildings).
    *   **`Solver/`**: Logic for calculating production chains (Linear Programming/Graph algorithms).
    *   **`Utils/`**: Helper utilities.
*   **`styles/`**: SCSS stylesheets.
*   **`templates/`**: HTML templates corresponding to Components and Controllers.
*   **`www/`**: Public output directory. Contains `index.html` and generated assets.

## Architecture & Key Concepts

### 1. AngularJS & TypeScript
The project uses AngularJS 1.x with TypeScript classes.
*   **Controllers/Services**: Defined as TypeScript classes and registered in `AppModule.ts`.
*   **Templates**: HTML files in `templates/` are required via Webpack (`require('@templates/...')`).

### 2. Routing (`AppModule.ts`)
Routing is handled by `angular-ui-router`.
*   **States**: Defined in `AppModule.ts`.
*   **URL Structure**: `/{version}/{tool}/...` (e.g., `/1.0/production`, `/1.0/items/iron-ore`).
*   **Version Handling**: The `version` parameter in the URL triggers `DataProvider.change(version)`, loading the appropriate JSON data.

### 3. Data Handling
*   **Static Data**: Data is not fetched from an API but imported from JSON files at build time (or lazy-loaded).
*   **`DataProvider`**: Singleton that holds the current version's data.
*   **`Data` class**: Provides helper methods to search/filter data (e.g., `getItemBySlug`, `getRecipesForItem`).

### 4. Production Solver
*   Located in `src/Solver/`.
*   Responsible for calculating the requirements for a production line given inputs/outputs.

## Development Workflow

### Prerequisites
*   Node.js (v14-v16 recommended, though v20+ works with `NODE_OPTIONS=--openssl-legacy-provider`).

### Commands
*   **Build (Production)**: `npm run build`
*   **Development (Watch + Serve)**: `npm run dev:watch`
    *   Runs Webpack in watch mode.
    *   Starts a local HTTP server at `http://localhost:8000`.
*   **Data Parsing**: Scripts in `bin/` (e.g., `npm run parseDocs`) are used to update `data/*.json` from game asset exports.

## Common Tasks

*   **Adding a new Page**:
    1.  Create Controller in `src/Module/Controllers/`.
    2.  Create Template in `templates/Controllers/`.
    3.  Register State in `AppModule.ts`.
    4.  Register Controller in `AppModule.ts`.

*   **Modifying Data Logic**:
    *   Edit `src/Data/Data.ts` for query logic.
    *   Edit `src/Schema/` if the data structure changes.

*   **Styling**:
    *   Edit `styles/style.scss` or component-specific SCSS files.
    *   Bootstrap 4 variables are customized in `styles/theme/`.

## Known Issues / Notes
*   **Legacy Sass**: The project uses older Sass syntax (`@import`). Expect deprecation warnings during build.
*   **Node Compatibility**: Requires `NODE_OPTIONS=--openssl-legacy-provider` on newer Node.js versions due to Webpack 4.
