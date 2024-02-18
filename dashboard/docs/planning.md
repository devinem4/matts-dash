# run planning

```js
const longitude = -75.05;
const latitude = 39.91;

async function json(url) {
  const response = await fetch(url);
  if (!response.ok) throw new Error(`fetch failed: ${response.status}`);
  return await response.json();
}

const station = await json(
  `https://api.weather.gov/points/${latitude},${longitude}`,
);
const forecast = await json(station.properties.forecastHourly);
display(forecast);
```

```js
const precip = forecast.properties.periods.map((d) => ({
  startTime: d.startTime,
  pop: d.probabilityOfPrecipitation.value,
}));
display(precip);
display(
  Plot.plot({
    title: "Hourly temperature forecast",
    x: { type: "utc", ticks: "day", label: null },
    y: { grid: true, inset: 10, label: "Degrees (F)" },
    marks: [
      Plot.lineY(forecast.properties.periods, {
        x: "startTime",
        y: "temperature",
        z: null, // varying color, not series
        stroke: "temperature",
        curve: "step-after",
      }),
      Plot.lineY(precip, {
        x: "startTime",
        y: "pop",
        z: null, // varying color, not series
        stroke: "pop",
        curve: "step-after",
      }),
    ],
  }),
);
```
