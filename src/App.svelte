<script lang="ts">
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let _svg: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  let svg = _svg as unknown | SVGElement;
  let drg: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  onMount(() => {
    _svg = d3.select("svg");

    _svg
      .attr("width", 400)
      .attr("height", 400)
      .attr("style", "background: #AAA");

    let rectData = [
      { x: 50, y: 50, width: 150, height: 60, color: "yellow" },
      { x: 100, y: 150, width: 150, height: 60, color: "lime" },
      { x: 150, y: 250, width: 150, height: 60, color: "red" },
    ];

    rectData.forEach((d, i) => {
      _svg
        .append("rect")
        .attr("x", d.x)
        .attr("y", d.y)
        .attr("width", d.width)
        .attr("height", d.height)
        .attr("class", "rect")
        .attr("id", "rect" + i)
        .style("fill", d.color)
        .on("mousedown", function (event: MouseEvent) {
          mousedown(event);
        })
        .on("mouseout", function (event: MouseEvent) {
          mouseout(event);
        });
    });

    drg = d3.selectAll("rect");
    drg.call(
      //DragEvent.dx and dy is not declared in Lib.dom.d.ts.
      //any.dx and dy is acceptable.
      d3
        .drag()
        .on("drag", function (event) {
          dragged(event);
        })
        .on("end", function (event) {
          dragended(event);
        })
    );
  });

  function mousedown(event: MouseEvent) {
    let event_source = (event.target as SVGElement).getAttribute("id");
    let target = _svg.selectAll("rect#" + event_source);
    target.attr("opacity", 0.47);
  }

  function mouseout(event: MouseEvent) {
    let event_source = (event.target as SVGElement).getAttribute("id");
    let target = _svg.selectAll("rect#" + event_source);
    target.attr("opacity", 1.0);
  }

  function dragged(event: any) {
    //DragEvent.dx and dy is not declared in Lib.dom.d.ts.
    //any.dx and dy is acceptable.
    let event_source = (event.sourceEvent.target as SVGElement).getAttribute(
      "id"
    );
    let target = _svg.selectAll("rect#" + event_source);
    if (target.size() == 1) {
      let x = Number(target.attr("x")) + event.dx;
      let y = Number(target.attr("y")) + event.dy;
      target.attr("x", x).attr("y", y);
    }
  }

  function dragended(event: any) {
    let event_source = (event.sourceEvent.target as SVGElement).getAttribute(
      "id"
    );
    let target = _svg.selectAll("rect#" + event_source);
    target.attr("opacity", 1.0);
  }
</script>

<svg bind:this={svg} />
