---
layout: page
permalink: /where/
title: Where is Ian?
nav: true
nav_order: 4
---

{% include custom-fonts.html %}
{% include theme-toggle.html %}

<style>
  .wi-panel {
    background: radial-gradient(120% 90% at 50% 0%, #563b9f 0%, #2a1a63 55%, #170f3b 100%);
    border-radius: 18px;
    padding: 1.25rem 1.25rem 1.5rem;
    color: #fff;
    box-shadow: 0 10px 30px rgba(23, 15, 59, 0.35);
  }
  .wi-head {
    text-align: center;
    min-height: 5.2rem;
  }
  .wi-date {
    display: inline-block;
    background: #fecd19;
    color: #1a1446;
    font-weight: 800;
    font-style: italic;
    text-transform: uppercase;
    letter-spacing: 0.04em;
    padding: 0.15rem 0.75rem;
    border-radius: 999px;
    font-size: 0.9rem;
  }
  .wi-city {
    color: #fff;
    font-size: 2rem;
    font-weight: 800;
    font-style: italic;
    text-transform: uppercase;
    line-height: 1.15;
    margin-top: 0.4rem;
    text-shadow: 0 3px 0 rgba(0, 0, 0, 0.25);
  }
  .wi-what {
    color: #d8ccff;
    font-size: 0.95rem;
  }
  .wi-globe {
    display: block;
    margin: 0.5rem auto 0;
    width: 100%;
    max-width: 460px;
    height: auto;
  }
  .wi-globe-wrap {
    position: relative;
    max-width: 460px;
    margin: 0 auto;
  }
  .wi-zoom {
    position: absolute;
    right: 0;
    bottom: 12%;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .wi-zoom button {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    border: 3px solid #fff;
    background: #fecd19;
    color: #1a1446;
    font-size: 1.3rem;
    font-weight: 800;
    line-height: 1;
    box-shadow: 0 3px 0 rgba(0, 0, 0, 0.3);
    cursor: pointer;
  }
  .wi-zoom button:active {
    transform: translateY(2px);
    box-shadow: 0 1px 0 rgba(0, 0, 0, 0.3);
  }
  .wi-slider-wrap {
    margin-top: 0.75rem;
  }
  .wi-slider {
    -webkit-appearance: none;
    appearance: none;
    width: 100%;
    height: 14px;
    border-radius: 999px;
    background: #3d2a85;
    outline: none;
    box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.35);
  }
  .wi-slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 28px;
    height: 28px;
    border-radius: 50%;
    background: #fecd19;
    border: 4px solid #fff;
    box-shadow: 0 3px 0 rgba(0, 0, 0, 0.3);
    cursor: pointer;
  }
  .wi-slider::-moz-range-thumb {
    width: 22px;
    height: 22px;
    border-radius: 50%;
    background: #fecd19;
    border: 4px solid #fff;
    box-shadow: 0 3px 0 rgba(0, 0, 0, 0.3);
    cursor: pointer;
  }
  .wi-ticks {
    position: relative;
    height: 1.2rem;
    margin: 0.35rem 14px 0;
    font-size: 0.75rem;
    font-weight: 700;
    font-style: italic;
    color: #b9a8f0;
  }
  .wi-ticks span {
    position: absolute;
    transform: translateX(-50%);
  }
</style>

<div class="wi-panel">
  <div class="wi-head">
    <span class="wi-date" id="wi-date"></span>
    <div class="wi-city" id="wi-city"></div>
    <div class="wi-what" id="wi-what"></div>
  </div>
  <div class="wi-globe-wrap">
    <svg class="wi-globe" id="wi-globe" viewBox="0 0 460 460" role="img" aria-label="Globe showing where Ian was"></svg>
    <div class="wi-zoom">
      <button type="button" id="wi-zoom-in" aria-label="Zoom in">+</button>
      <button type="button" id="wi-zoom-out" aria-label="Zoom out">&minus;</button>
    </div>
  </div>
  <div class="wi-slider-wrap">
    <input class="wi-slider" id="wi-slider" type="range" min="0" step="1" aria-label="Date" />
    <div class="wi-ticks" id="wi-ticks"></div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/7.9.0/d3.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/topojson-client@3.1.0/dist/topojson-client.min.js"></script>
<script>
  (function () {
    var places = {{ site.data.whereabouts | jsonify }};
    var START = { y: 2012, m: 1 };
    var now = new Date();
    var nowIdx = (now.getFullYear() - START.y) * 12 + now.getMonth();

    function key(s) {
      var p = String(s).split("-");
      return +p[0] * 12 + (+p[1] - 1);
    }
    function idxKey(i) {
      return START.y * 12 + (START.m - 1) + i;
    }
    function placeAt(i) {
      var k = idxKey(i);
      for (var j = places.length - 1; j >= 0; j--) {
        var p = places[j];
        if (k >= key(p.start) && (!p.end || k <= key(p.end))) return p;
      }
      return places[0];
    }
    function label(i) {
      var k = idxKey(i);
      if (k < 2013 * 12) return "Before 2013";
      return String(Math.floor(k / 12));
    }

    var svg = d3.select("#wi-globe");
    var W = 460, R = 210;
    var projection = d3.geoOrthographic().scale(R).translate([W / 2, W / 2]).clipAngle(90);
    var path = d3.geoPath(projection);
    var graticule = d3.geoGraticule10();

    svg.append("defs").append("clipPath").attr("id", "wi-clip").append("circle").attr("cx", W / 2).attr("cy", W / 2).attr("r", R);
    svg.append("circle").attr("cx", W / 2).attr("cy", W / 2 + 8).attr("r", R).attr("fill", "rgba(0,0,0,0.3)");
    var globeG = svg.append("g").attr("clip-path", "url(#wi-clip)");
    globeG.append("path").datum({ type: "Sphere" }).attr("class", "sphere").attr("fill", "#4fa3e8");
    var gridPath = globeG.append("path").datum(graticule).attr("fill", "none").attr("stroke", "rgba(255,255,255,0.18)").attr("stroke-width", 0.6);
    var landPath = globeG.append("path").attr("fill", "#8fd16a").attr("stroke", "#4e8a3a").attr("stroke-width", 0.8);
    var dotsG = globeG.append("g");
    var pinG = globeG.append("g").style("pointer-events", "none");
    svg.append("circle").attr("cx", W / 2).attr("cy", W / 2).attr("r", R).attr("fill", "none").attr("stroke", "#fff").attr("stroke-width", 3).style("pointer-events", "none");
    var zoomK = 1;

    var cities = [];
    places.forEach(function (p) {
      if (!cities.some(function (c) { return c.city === p.city; })) cities.push(p);
    });

    var center = [0, 0];
    var current = null;

    function visible(lonlat) {
      return d3.geoDistance(lonlat, [-projection.rotate()[0], -projection.rotate()[1]]) < Math.PI / 2 - 0.02;
    }

    function draw() {
      svg.selectAll("path.sphere").attr("d", path);
      gridPath.attr("d", path);
      if (landPath.datum()) landPath.attr("d", path);
      var dots = dotsG.selectAll("g.wi-dot").data(cities.filter(function (c) { return visible([c.lon, c.lat]); }), function (d) { return d.city; });
      dots.exit().remove();
      var entered = dots.enter().append("g").attr("class", "wi-dot").style("cursor", "pointer").on("click", function (e, d) { jumpTo(d.city); });
      entered.append("title").text(function (d) { return d.city + ", " + d.country; });
      entered.append("circle").attr("r", 12).attr("fill", "transparent");
      entered.append("circle").attr("r", 5).attr("fill", "#fecd19").attr("stroke", "#1a1446").attr("stroke-width", 1.5);
      entered.merge(dots).attr("transform", function (d) { return "translate(" + projection([d.lon, d.lat]) + ")"; });
      pinG.selectAll("*").remove();
      if (current && visible([current.lon, current.lat])) {
        var xy = projection([current.lon, current.lat]);
        var g = pinG.append("g").attr("transform", "translate(" + xy[0] + "," + xy[1] + ")");
        g.append("ellipse").attr("cx", 0).attr("cy", 2).attr("rx", 7).attr("ry", 3).attr("fill", "rgba(0,0,0,0.35)");
        g.append("path").attr("d", "M0,0 C-4,-10 -13,-16 -13,-26 A13,13 0 1,1 13,-26 C13,-16 4,-10 0,0 Z")
          .attr("fill", "#e8303f").attr("stroke", "#fff").attr("stroke-width", 3);
        g.append("circle").attr("cx", 0).attr("cy", -26).attr("r", 5).attr("fill", "#fff");
      }
    }

    function goTo(p) {
      current = p;
      var target = [p.lon, p.lat];
      var from = center.slice();
      var interp = d3.geoInterpolate(from, target);
      var dist = d3.geoDistance(from, target);
      svg.interrupt().transition().duration(400 + 900 * (dist / Math.PI)).ease(d3.easeCubicInOut)
        .tween("rotate", function () {
          return function (t) {
            center = interp(t);
            projection.rotate([-center[0], -center[1]]);
            draw();
          };
        });
    }

    var slider = document.getElementById("wi-slider");
    slider.max = nowIdx;
    slider.value = nowIdx;

    var ticks = document.getElementById("wi-ticks");
    for (var y = 2013; y <= now.getFullYear(); y += 2) {
      var i = (y - START.y) * 12;
      var s = document.createElement("span");
      s.style.left = (100 * i) / nowIdx + "%";
      s.textContent = y;
      ticks.appendChild(s);
    }

    var lastCity = null;
    function update() {
      var i = +slider.value;
      var p = placeAt(i);
      document.getElementById("wi-date").textContent = label(i);
      document.getElementById("wi-city").textContent = p.city + ", " + p.country;
      document.getElementById("wi-what").textContent = p.what;
      if (p !== lastCity) {
        lastCity = p;
        goTo(p);
      }
    }
    slider.addEventListener("input", update);

    function jumpTo(city) {
      var starts = places
        .filter(function (p) { return p.city === city; })
        .map(function (p) { return Math.max(0, key(p.start) - idxKey(0)); });
      var cur = +slider.value;
      var next = starts.find(function (i) { return i > cur && placeAt(i).city === city && placeAt(cur) !== placeAt(i); });
      slider.value = next !== undefined ? next : starts[0];
      update();
    }

    var DEG_PER_PX = 180 / (Math.PI * R);
    var hiRes = false;
    function setZoom(k) {
      zoomK = Math.max(1, Math.min(6, k));
      projection.scale(R * zoomK);
      draw();
      if (zoomK > 2 && !hiRes) {
        hiRes = true;
        d3.json("https://cdn.jsdelivr.net/npm/world-atlas@2.0.2/land-50m.json").then(function (world) {
          landPath.datum(topojson.feature(world, world.objects.land));
          draw();
        });
      }
    }
    svg.node().addEventListener(
      "wheel",
      function (e) {
        e.preventDefault();
        setZoom(zoomK * Math.exp(-e.deltaY * 0.002));
      },
      { passive: false }
    );
    document.getElementById("wi-zoom-in").addEventListener("click", function () { setZoom(zoomK * 1.6); });
    document.getElementById("wi-zoom-out").addEventListener("click", function () { setZoom(zoomK / 1.6); });
    svg.style("cursor", "grab").call(
      d3.drag()
        .clickDistance(4)
        .on("start", function () { svg.interrupt().style("cursor", "grabbing"); })
        .on("drag", function (e) {
          var d = DEG_PER_PX / zoomK;
          center = [center[0] - e.dx * d, Math.max(-80, Math.min(80, center[1] + e.dy * d))];
          projection.rotate([-center[0], -center[1]]);
          draw();
        })
        .on("end", function () { svg.style("cursor", "grab"); })
    );

    var p0 = placeAt(nowIdx);
    center = [p0.lon, p0.lat];
    projection.rotate([-center[0], -center[1]]);
    current = p0;
    lastCity = p0;
    update();
    draw();

    d3.json("https://cdn.jsdelivr.net/npm/world-atlas@2.0.2/land-110m.json").then(function (world) {
      if (hiRes) return;
      landPath.datum(topojson.feature(world, world.objects.land));
      draw();
    });
  })();
</script>
