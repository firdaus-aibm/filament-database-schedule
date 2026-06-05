# AGENTS.md — filament-database-schedule

A Filament 3.x plugin (`husam-tariq/filament-database-schedule`) that stores Laravel task schedules in the DB and manages them via a Filament resource.

## Commands

| What | Command |
|---|---|
| Run all tests | `composer test` (runs pest --parallel then phpstan) |
| Run pest only | `vendor/bin/pest` |
| Dev watch (CSS/JS) | `npm run dev` |
| Build assets | `npm run build` |
| Publish migrations | `php artisan vendor:publish --tag="filament-database-schedule-migrations"` |
| Publish config | `php artisan vendor:publish --tag="filament-database-schedule-config"` |

## Key architecture

- **PHP 8.1+**, Laravel 8–11, **Filament 3.x**.
- Service provider extends `Spatie\LaravelPackageTools\PackageServiceProvider`.
- Plugin class (`FilamentDatabaseSchedulePlugin`) implements `Filament\Contracts\Plugin`; register via `$panel->plugins([FilamentDatabaseSchedulePlugin::make()])`.
- Namespace: `HusamTariq\FilamentDatabaseSchedule\` → `src/`, tests → `tests/`.
- `Schedule` model uses `Illuminate\Console\Scheduling\ManagesFrequencies` trait and `SoftDeletes`.
- `Schedule::observe(ScheduleObserver::class)` + `ScheduleHistory::observe(ScheduleHistoryObserver::class)` booted in the service provider.
- Three artisan commands: `TestJobCommand`, `PhpUnitTestJobCommand`, `ScheduleClearCacheCommand`.

## Testing

- **Pest PHP 2.x** (not plain PHPUnit) with `pest-plugin-laravel` and `pest-plugin-livewire`.
- Test base: `Orchestra\Testbench\TestCase`; in-memory SQLite (`database.default => testing`).
- Single test command: `vendor/bin/pest` — no need for phpunit.xml, the `.dist` file handles config.
- CI runs `vendor/bin/pest` directly, not through `composer test` (avoids phpstan in CI).
- `composer.lock` is gitignored; `phpunit.xml` is gitignored (use `phpunit.xml.dist`).

## Frontend

- **TailwindCSS 3.x**, **esbuild**, **PostCSS**, **Prettier** (no semicolons, single quotes, trailing commas).
- Input: `resources/css/plugin.css` + `resources/js/plugin.js`
- Output: `resources/dist/filament-database-schedule.{css,js}`
- Build runs `filament-purge` on the CSS (`npm run purge`).
- Tailwind content paths: `./resources/views/**/*.blade.php`, `./src/**/*.php`. Dark mode: `class`. Preflight disabled.

## Config

- `config/filament-database-schedule.php` controls model/table names, timezone, cache, resources, command whitelist/blacklist, pagination.
- Resources are registered dynamically via `config('filament-database-schedule.resources')` in the plugin.
- Env vars: `FILAMENT_SCHEDULE_TIMEZONE`, `FILAMENT_SCHEDULE_CACHE_DRIVER` (default `file`), `FILAMENT_SCHEDULE_CACHE_ENABLE` (default `!app.debug`), `FILAMENT_SCHEDULE_HISTORY_COLLAPSED`.

## Code style

- **PSR-2** (per CONTRIBUTING.md, though PSR-12 is the modern equivalent).
- `.editorconfig`: 4-space indent (PHP/most), 2-space (YAML), LF line endings, UTF-8.
