# Project-1
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bar Chart Project</title>
    <script src="https://d3js.org/d3.v7.min.js"></script>
    <style>
        /* Add your CSS styles here */
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }

        h1 {
            margin-bottom: 20px;
        }

        #chart {
            display: block;
            margin: 0 auto;
        }

        .bar {
            fill: steelblue;
        }

        #tooltip {
            position: absolute;
            background-color: lightgray;
            padding: 5px;
            border-radius: 5px;
            display: none;
            font-size: 14px;
        }

        .tick text {
            font-size: 12px;
        }
    </style>
</head>
<body>
    <h1 id="title">GDP Bar Chart</h1>
    <svg id="chart"></svg>
    <div id="tooltip"></div>

    <!-- Load the FCC Test Script -->
    <script src="https://cdn.freecodecamp.org/testable-projects-fcc/v1/bundle.js"></script>

    <script>
        const url = "https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/GDP-data.json";

        // Set dimensions for the chart
        const margin = { top: 50, right: 30, bottom: 50, left: 60 };
        const width = 800 - margin.left - margin.right;
        const height = 400 - margin.top - margin.bottom;

        const svg = d3.select("#chart")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
          .append("g")
            .attr("transform", `translate(${margin.left}, ${margin.top})`);

        // Load the data
        d3.json(url).then(data => {
            const dataset = data.data;

            // Define the scales
            const xScale = d3.scaleBand()
                .domain(dataset.map(d => new Date(d[0])))
                .range([0, width])
                .padding(0.1);

            const yScale = d3.scaleLinear()
                .domain([0, d3.max(dataset, d => d[1])])
                .range([height, 0]);

            // Define the axes
            const xAxis = d3.axisBottom(xScale).tickFormat(d3.timeFormat("%Y"));
            const yAxis = d3.axisLeft(yScale);

            // Append x-axis
            svg.append("g")
                .attr("id", "x-axis")
                .attr("transform", `translate(0, ${height})`)
                .call(xAxis);

            // Append y-axis
            svg.append("g")
                .attr("id", "y-axis")
                .call(yAxis);

            // Create bars
            svg.selectAll(".bar")
                .data(dataset)
                .enter()
                .append("rect")
                .attr("class", "bar")
                .attr("x", d => xScale(new Date(d[0])))
                .attr("y", d => yScale(d[1]))
                .attr("width", xScale.bandwidth())
                .attr("height", d => height - yScale(d[1]))
                .attr("data-date", d => d[0])
                .attr("data-gdp", d => d[1])
                .on("mouseover", (event, d) => {
                    const tooltip = d3.select("#tooltip");
                    tooltip.style("display", "block")
                        .style("left", `${event.pageX + 5}px`)
                        .style("top", `${event.pageY - 28}px`)
                        .attr("data-date", d[0])
                        .html(`Date: ${d[0]}<br>GDP: ${d[1]}`);
                })
                .on("mouseout", () => {
                    d3.select("#tooltip").style("display", "none");
                });
        });
    </script>
</body>
</html>
