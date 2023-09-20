<script>
  import { onMount } from "svelte";

  import * as vg from "@uwdata/vgplot";

  let chartElement;

  let rightHeight = 135;
  let strokeColor = "black";

  onMount(async () => {
    await vg
      .coordinator()
      .exec(
        vg.loadCSV(
          "penguins",
          "https://raw.githubusercontent.com/uwdata/mosaic/main/data/penguins.csv"
        )
      );

    const $domainSex = vg.Param.array(["MALE", "FEMALE"]);
    const $domainIsland = vg.Param.array(["Torgersen", "Biscoe", "Dream"]);
    const $domainSpecies = vg.Param.array(["Adelie", "Chinstrap", "Gentoo"]);
    const $colors = vg.Param.array(["skyblue", "coral", "teal"]);

    const $brush = vg.Selection.single();
    const $bandwidth = vg.Param.value(10);

    $brush.addEventListener("activate", (a) => {
      console.log("Activate!", a);
    });

    const chart = vg.vconcat(
      vg.hconcat(
        vg.vconcat(
          vg.plot(
            vg.voronoi(vg.from("penguins", { filterBy: $brush }), {
              x: "bill_length",
              y: "bill_depth",
              stroke: "black",
              strokeWidth: 1,
              inset: 1,
              fill: "species",
              fillOpacity: 0.46,
            }),
            vg.dot(vg.from("penguins"), {
              x: "bill_length",
              y: "bill_depth",
              fill: "species",
              r: 5.5,
              stroke: "white",
              strokeWidth: 1,
              tip: true,
            }),
            vg.inset(4),
            vg.width(750),
            vg.height(650),
            vg.intervalXY({
              as: $brush,
              brush: { fill: "none", stroke: "black", strokeWidth: 3 },
            }),
            vg.highlight({ by: $brush, opacity: 0.1 }),
            vg.colorDomain($domainSpecies),
            vg.colorRange($colors)
          )
        ),
        vg.vconcat(
          vg.hconcat(
            vg.plot(
              vg.barX(vg.from("penguins", { filterBy: $brush }), {
                x: vg.count(),
                y: "species",
                fill: "species",
                order: "species",
                stroke: strokeColor,
                strokeWidth: 3,
              }),
              vg.xDomain(vg.Fixed),
              vg.yDomain($domainSpecies),
              vg.yLabel(null),
              vg.colorDomain($domainSpecies),
              vg.colorRange($colors),
              vg.marginLeft(60),
              vg.width(300),
              vg.height(rightHeight)
            ),
            vg.plot(
              vg.barX(vg.from("penguins", { filterBy: $brush }), {
                x: vg.count(),
                y: "sex",
                fill: "sex",
                order: "sex",
                stroke: strokeColor,
                strokeWidth: 3,
              }),
              vg.xDomain(vg.Fixed),
              vg.yDomain($domainSex),
              vg.yLabel(null),
              vg.colorDomain($domainSex),
              vg.colorRange($colors),
              vg.marginLeft(60),
              vg.width(300),
              vg.height(rightHeight)
            ),
            vg.plot(
              vg.barX(vg.from("penguins", { filterBy: $brush }), {
                x: vg.count(),
                y: "island",
                fill: "black",
                fillOpacity: 0.1,
                order: "island",
                stroke: strokeColor,
                strokeWidth: 3,
              }),
              vg.xDomain(vg.Fixed),
              vg.yDomain($domainIsland),
              vg.yLabel(null),
              vg.colorDomain($domainIsland),
              vg.marginLeft(60),
              vg.width(300),
              vg.height(rightHeight)
            )
          ),
          vg.plot(
            vg.densityY(vg.from("penguins", { filterBy: $brush }), {
              x: "bill_depth",
              fill: "black",
              fillOpacity: 0.1,
              bandwidth: $bandwidth,
              stroke: strokeColor,
              strokeWidth: 3,
            }),
            vg.intervalX({ as: $brush }),
            vg.yAxis(null),
            vg.xDomain(vg.Fixed),
            vg.width(600),
            vg.marginLeft(10),
            vg.height(rightHeight)
          ),
          vg.plot(
            vg.densityY(vg.from("penguins", { filterBy: $brush }), {
              x: "bill_length",
              fill: "black",
              fillOpacity: 0.1,
              bandwidth: $bandwidth,
              stroke: strokeColor,
              strokeWidth: 3,
            }),
            vg.intervalX({ as: $brush }),
            vg.yAxis(null),
            vg.xDomain(vg.Fixed),
            vg.width(600),
            vg.marginLeft(10),
            vg.height(rightHeight)
          ),
          vg.plot(
            vg.densityY(vg.from("penguins", { filterBy: $brush }), {
              x: "flipper_length",
              fill: "black",
              fillOpacity: 0.1,
              bandwidth: $bandwidth,
              stroke: strokeColor,
              strokeWidth: 3,
            }),
            vg.intervalX({ as: $brush }),
            vg.yAxis(null),
            vg.xDomain(vg.Fixed),
            vg.width(600),
            vg.marginLeft(10),
            vg.height(rightHeight)
          )
        )
      )
    );

    chartElement.appendChild(chart);
  });
</script>

<div class="container">
  <div bind:this={chartElement} />
</div>
