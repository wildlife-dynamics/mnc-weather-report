# MNC Weather Report

Retrieves weather station observations from EarthRanger and produces daily summary tables and interactive time-series charts for seven meteorological variables.

## What it produces

- **1 daily weather summary CSV** &mdash; precipitation, temperature, wind speed, wind gusts, soil temperature, relative humidity, and atmospheric pressure aggregated per weather station and date
- **7 line charts (HTML + PNG)** &mdash; one per meteorological variable, coloured by weather station, covering the full analysis time range
- **An interactive dashboard** &mdash; one plot widget per chart (7 total)

## Requirements

- Access to an **EarthRanger** instance with weather station observations recorded under the subject group **ER2ER - From GMMF** for the analysis period (this subject group name is fixed and not user-configurable)

---

## 1. Load the Workflow

In the workflow runner, go to **Workflow Templates** and click **Add Workflow Template**. Paste this repository's URL into the **Github Link** field, then click **Add Template**:

```
https://github.com/wildlife-dynamics/mnc-weather-report.git
```

Once added, it appears in the **Workflow Templates** list as **mnc_weather_report**. Click the card to open the workflow configuration form.

---

## 2. Configure the Workflow

### Data Source Connection

Navigate to **Data Sources** and click **Connect**, then select **EarthRanger**. Fill in the connection form:

| Field | Description |
|-------|-------------|
| Data Source Name | A label to identify this connection (e.g. `Mara North Conservancy`) |
| EarthRanger URL | Your instance URL (e.g. `your-site.pamdas.org`) |
| EarthRanger Username | Your EarthRanger username |
| EarthRanger Password | Your EarthRanger password |

> Credentials are not validated at setup time. Any authentication errors will appear when the workflow runs.

Click **Connect** to save.

### Workflow Details

| Field | Description |
|-------|-------------|
| Workflow Name | A short name to identify this run |
| Workflow Description | Optional notes (e.g. reporting month or site) |

### Time Range

| Field | Description |
|-------|-------------|
| Timezone | Select the local timezone (e.g. `Africa/Nairobi UTC+03:00`) |
| Since | Start date and time &mdash; weather observations from this point are fetched |
| Until | End date and time of the analysis window |

### Connect to ER

Select the EarthRanger data source configured above from the **Data Source** dropdown (e.g. `Mara North Conservancy`).

Once all sections are filled, click **Submit**.

---

## 3. Run the Workflow

Once submitted, the runner will:

1. Fetch all observations from the subject group **ER2ER - From GMMF** for the analysis period.
2. Extract seven meteorological variables from each observation's JSON detail field: precipitation, surface air temperature, wind speed, wind gusts, soil temperature, relative humidity, and atmospheric pressure.
3. Compute daily aggregates per weather station &mdash; summing precipitation and averaging each remaining variable, except wind gusts which takes the daily maximum.
4. Persist the daily summary as `weather_summary_table.csv`.
5. Draw seven interactive line charts (one per variable), coloured by weather station.
6. Convert all seven charts to PNG at 1 280 &times; 720 px (2&times; scale).
7. Assemble the dashboard with one plot widget per chart.
8. Save all outputs to the directory specified by `ECOSCOPE_WORKFLOWS_RESULTS`.

### Output Files

All outputs are written to `$ECOSCOPE_WORKFLOWS_RESULTS/`.

| File | Description |
|------|-------------|
| `weather_summary_table.csv` | Daily per-station summary: sum precipitation, mean temperature / wind / humidity / pressure, max gusts |
| `precipitation_readings_over_time.html` / `.png` | Daily total precipitation (mm) per weather station |
| `temperature_readings_over_time.html` / `.png` | Daily mean surface air temperature (&deg;C) per weather station |
| `wind_speed_readings_over_time.html` / `.png` | Daily mean wind speed per weather station |
| `wind_gusts_readings_over_time.html` / `.png` | Daily maximum wind gusts per weather station |
| `soil_temperature_readings_over_time.html` / `.png` | Daily mean soil temperature (&deg;C) per weather station |
| `relative_humidity_readings_over_time.html` / `.png` | Daily mean relative humidity per weather station |
| `atmospheric_pressure_readings_over_time.html` / `.png` | Daily mean atmospheric pressure per weather station |

---

## More Help

- **Technical Guide:** [technical_guide/mnc_weather_report_technical_guide.pdf](technical_guide/mnc_weather_report_technical_guide.pdf) &mdash; pipeline internals and task-by-task reference
- **Issues:** [github.com/wildlife-dynamics/mnc-weather-report/issues](https://github.com/wildlife-dynamics/mnc-weather-report/issues)

## Development

This workflow's code (`ecoscope-workflows-mnc-weather-report-workflow/`) is generated from [`spec.yaml`](spec.yaml) and [`test-cases.yaml`](test-cases.yaml). After editing either file, recompile and commit the generated changes:

```
pixi run --manifest-path pixi.toml --locked bash -c "./dev/recompile.sh --update"
```

## License

[BSD 3-Clause](LICENSE)
