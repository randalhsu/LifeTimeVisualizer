# Your Life Time

Visualize the progress of a life.

## Overview

* Parameters: "birthday" and "life expectancy"
* Each row is 52 dots, representing the 52 weeks of a year (W)
* Rows represent years, one per year of life expectancy (Y)
* The page renders W * Y dots
* Supports URL params `?birthday=2000-01-01&expectancy=80` to pass in birthday and life expectancy
* Supports URL param `theme` to select a theme: `paper` (default), `auto` (follows system light/dark), `dark`, `light`, `sepia`, `ocean`, `sky`, `forest`, `mint`, `midnight`, `sakura`; the form provides a dropdown, switching applies immediately and syncs the URL
* Supports URL param `shape` to select the week mark: `dot` (default, circle) or `square` (slightly rounded); the form provides a dropdown, switching applies immediately and syncs the URL
* Bilingual UI: English by default; shows Traditional Chinese when the browser language is Chinese (`zh*`); URL param `lang=en|zh` forces the language
* Form sits at the bottom of the page, dot grid above

## Tech stack

* Pure static single `index.html`, inline CSS/JS, zero dependencies, works by opening the file

## Progress rendering

* Each row represents one year of age, starting from the birthday (row 0 = the year at age 0), not a calendar year
* Past weeks: filled; future weeks: hollow; current week: highlighted
* Current row = current age (number of birthdays passed)
* Filled dots in the current row = `min(51, floor(days since last birthday / 7))`; that dot is the current week
* A Feb 29 birthday rolls over to Mar 1 in non-leap years via JS Date; accepted as is

## Grouping and layout

* Horizontal: each row of 52 weeks is separated into groups of 13, 4 groups total, visually matching 4 quarters
* Vertical: a band every 5 years (larger row spacing)
* Dots are rendered on a single `<canvas>`, sized in device pixels (devicePixelRatio-aware)

## RWD

* All dots must fit within one page, no scrollbars
* Dot size = the largest size where the whole grid (dots, gaps, group spacing, bands, halo padding) fits the stage area (viewport minus form and page padding), by both width and height; recomputed on resize
* Dot size and all gaps are snapped to integer device px so spacing renders evenly with minimal leftover space

## Form and URL

* Form: `<input type="date">` birthday + `<input type="number">` life expectancy
* URL has both `birthday` and `expectancy` params and they validate: prefill and render, form hidden by default
* Missing either param: form shown by default, rendered with defaults `2000-01-01` and `80`
* Invalid params: form shown with the error message, no grid
* Clicking/touching anywhere on the page (except the form itself) toggles the form, regardless of how it started
* Birthday/expectancy update live on `input` (no submit button): valid values sync the URL via `history.replaceState` and re-render; invalid mid-typing values silently keep the last render, showing the error only on `change` (blur / picker commit)

## Image download

* A Download button in the form exports the rendered canvas as a PNG
* The exported image has an opaque theme background (`--bg`) and a margin of 4% of the canvas width on all sides
* Resolution equals the canvas backing store (device pixels), so exports are sharp on high-DPI screens
* Filename: `life-in-weeks-YYYY-MM-DD.png`, suffixed with the local date
* If nothing is rendered (validation error), the button does nothing

## Validation

* Birthday cannot be in the future
* Life expectancy must be an integer between 1 and 100
* Invalid format or out of range: show an error message next to the form, do not render the grid
