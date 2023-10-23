---
title: "Team Awards"
layout: post
date: 2015-10-19 22:48
image: /assets/images/profile.png
headerImage: false
tag:
- markdown
- components
- extra
category: blog
author: jamesfoster 
description: Get the list of teams' awards they've won
---

# Team Awards
> Get any team's awards

On this page, you can:

- Enter any FRC team's team number and get all the awards they've ever won any year.
- Parse through the data specifically for our Outreach department
- Compare other teams' data to our (Team Optix 3749) own.
    * If there is a note at the bottom about either regional winning, outreach, or engineering inspiration, it is because the amount of times the desired team has won those awards is greater than or equal to our own.

Limitations (as of 10/19/2023):

- You must refresh the page every time you want a new team's data. If this is not done, data will stack and you won't be able to tell whose data is whose.

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Team Awards</title>
    <style>
        input[type="text"] {
            padding: 5px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #0bbcd2;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-right: 10px;
        }
        p, table, thead, tr, th, td, tbody {
            color: white;
        }
        table {
            border-collapse: collapse;
            width: 100%;
            margin-top: 20px;
        }
        thead {
            background-color: #0b2262;
            color: white;
        }
        th, td {
            border: 1px solid #091b4f;
            padding: 8px;
            text-align: left;
        }
        tr:nth-child(even) {
            background-color: #091b4f;
        }
        .winner-background {
            background-color: #FFA500;
        }
        .impact-background {
            background-color: #61C0BF;
        }
        .rei-background {
            background-color: green;
        }
    </style>
</head>
<body>
    <p>Enter a team number:</p>
    <input id="teamNumber" type="text">
    <br><br>
    <button onclick="fetchAwards()">Get Awards</button>
    <button onclick="parseAwards()">Parse Awards</button>
    <button onclick="compareAwards()">Compare to Optix Stats</button>
    <table id="data-table">
        <thead>
            <tr>
                <th>Award Name</th>
                <th>Event Name</th>
                <th>Year</th>
            </tr>
        </thead>
        <tbody>
            <!-- Data will be displayed here -->
        </tbody>
    </table>
    <p id="winnerReport"></p>
    <p id="impactReport"></p>
    <p id="reiReport"></p>
    <script>
        const optixRegional = 0;
        const optixImpact = 2;
        const optixREI = 1;
        let regionalWinners = 0;
        let impactWinners = 0;
        let reiWinners = 0;
        let teamNumber;
        function fetchAwards() {
            teamNumber = document.getElementById("teamNumber").value;
            const dataTable = document.getElementById("data-table");
            const tbody = dataTable.querySelector("tbody");
            tbody.innerHTML = "";
            const apiKey = "IJGdHobToWBkfqCzNHRKGWKyy66mMiOl7A7IOs1WjcgfS4d6sIryBqQWsALTPTVv";
            const apiUrl = "https://www.thebluealliance.com/api/v3";
            const teamKey = "frc" + teamNumber;
            const endpoint = `/team/${teamKey}/awards`;
            const requestUrl = `${apiUrl}${endpoint}`;
            fetch(requestUrl, {
                headers: {
                    "X-TBA-Auth-Key": apiKey,
                }
            })
            .then(response => response.json())
            .then(data => {
                if (Array.isArray(data)) {
                    console.log(data);
                    data.forEach(award => {
                        const row = dataTable.insertRow();
                        const awardNameCell = row.insertCell(0);
                        const eventNameCell = row.insertCell(1);
                        const yearCell = row.insertCell(2);
                        awardNameCell.textContent = award.name;
                        eventNameCell.textContent = award.event_name;
                        yearCell.textContent = award.year;
                        if (award.name.includes("Regional Winners") || award.name.includes("Winner")) {
                            regionalWinners++;
                        } else if (award.name.includes("Regional Chairman's Award") || award.name.includes("Regional FIRST Impact Award")) {
                            impactWinners++;
                        } else if (award.name.includes("Regional Engineering Inspiration Award")) {
                            reiWinners++;
                        }
                    });
                    parseAwards();
                    compareAwards();
                } else {
                    console.error("Data received from the API is not an array.");
                }
            })
            .catch(error => {
                console.error("Error fetching data:", error);
            });
        }
        function parseAwards() {
            const dataTable = document.getElementById("data-table");
            const rows = dataTable.querySelectorAll("tr");
            rows.forEach(row => {
                const cells = row.querySelectorAll("td");
                cells.forEach(cell => {
                    const text = cell.textContent;
                    if (text.includes("Regional Winners") || text.includes("Winner")) {
                        cell.classList.add("winner-background");
                    } else if (text.includes("Regional Chairman's Award") || text.includes("Regional FIRST Impact Award")) {
                        cell.classList.add("impact-background");
                    } else if (text.includes("Regional Engineering Inspiration Award")) {
                        cell.classList.add("rei-background");
                    }
                });
            });
        }
        function compareAwards() {
            teamNumber = document.getElementById("teamNumber").value;
            if (regionalWinners >= optixRegional) {
                document.getElementById("winnerReport").innerHTML = "We would benefit from scouting team " + teamNumber + "'s regional winning/robotics performance."
            }
            if (impactWinners >= optixImpact) {
                document.getElementById("impactReport").innerHTML = "We would benefit from scouting team " + teamNumber + "'s outreach."
            }
            if (reiWinners >= optixREI) {
                document.getElementById("reiReport").innerHTML = "We would benefit from scouting team " + teamNumber + "'s engineering inspiration."
            }
        }
    </script>
</body>
</html>
