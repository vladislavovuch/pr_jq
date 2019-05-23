<template>
    <div class="app">
        <div class="search">
            <input id="datepicker">
            <!--</input>-->
            <button>Search</button>
        </div>
        <div id="table">
            <table id="myTable" class="tablesorter">

            </table>
        </div>
    </div>
</template>

<script>
    import $ from 'jquery'
    import 'tablesorter'
    import datepickerFactory from 'jquery-datepicker';
    // import datepickerJAFactory from 'jquery-datepicker/i18n/jquery.ui.datepicker-ja';

    // Just pass your jquery instance and you're done
    datepickerFactory($);
    // datepickerJAFactory($);

    export default {
        mounted() {

            function changeRoutes(routes) {
                for (let i = 0; i < routes.length; i++) {
                    for (const key in routes[i]) {
                        routes[i][key] = {
                            text: routes[i][key],
                            marked: false,
                        }
                    }
                }
                return routes;
            }

            let routes = null;

            function createTableHeader(routes) {
                const thead = document.createElement('thead');
                const tr = document.createElement('tr');
                thead.appendChild(tr);
                for (const key in routes[0]) {
                    const th = document.createElement('th');
                    th.innerText = key;
                    th.dataset.field = key;
                    tr.appendChild(th);
                }
                return thead;
            }

            function createTableContent(routes, table) {
                const tbody = document.createElement('tbody');
                for (let i = 0; i < routes.length; i++) {
                    const route = routes[i];
                    console.log(route);
                    const tr = document.createElement('tr');
                    for (const item in route) {
                        const td = document.createElement('td');
                        td.innerText = route[item].text;
                        if (route[item].marked) {
                            td.classList.add('marked')
                        }
                        tr.appendChild(td);
                    }
                    console.log(table);
                    tbody.appendChild(tr);
                }
                table.appendChild(tbody);
            }
            //
            // function createTable(routes) {
            //     //get table
            //     const table = document.createElement('table');
            //     //create th
            //     const tableHeader = createTableHeader(routes);
            //     table.appendChild(tableHeader);
            //
            //     // create tr + td
            //     createTableContent(routes, table);
            //
            //     //add listener
            //     // table.onclick = tableSort.bind(routes);
            //
            //     //add created element into dom
            //     document.getElementById('table').appendChild(table);
            // }
            //


            function getRoutes() {
                fetch('http://localhost:3000/routes')
                    .then(response => {
                        console.log(response)
                        if (response.status !== 200) {
                            throw new Error(response.statusText);
                        }
                        return response.json();
                    })
                    .then(response => {
                        console.log(response);
                        response = changeRoutes(response);
                        routes = response;
                        createTable(response);

                    })
                    .catch(err => {
                        console.warn(err)
                    })
            }

            getRoutes();
            //
            function reRenderTable(routes) {
                const div = document.getElementById('table');
                div.removeChild(div.firstChild);
                const table = document.createElement('table')
                div.appendChild(table);
                createTable(routes);
            }

            // let lastSortedKey = null;

            function createTable(routes) {
                const table = document.querySelector('table');

                const tableHeader = createTableHeader(routes);
                table.appendChild(tableHeader);

                createTableContent(routes, table);


                $(function () {
                    $("table").tablesorter();
                });
            }


            'use strict'

            let weekDay = -1;

            $(function() {
                $('#datepicker').datepicker({
                    // showWeek: true,
                    onSelect: function(dateText, inst) {
                        // var month = dateText.getUTCMonth();
                        var date = $(this).datepicker('getDate'),
                            day  = date.getDay();
                            // var year = dateText.getUTCFullYear();
                        // alert(day+month+year);
                        weekDay = day;
                        debugger;
                    }
                });
                $('.search button').on('click', searchRoutes)
            });

            const WEEK_DAYS = ['Нд','Пн','Вт','Ср','Чт','Пт','Сб',];

            function searchRoutes() {
                // const value = $('#datepicker').datepicker().val();
                console.log(WEEK_DAYS[weekDay]);
                if (weekDay >= 0) {
                    let newRoutes = routes.filter(route => {
                        console.log(route.weekday.text)
                        if (route.weekday.text === WEEK_DAYS[weekDay]) {
                            return true;
                        } else {
                            return false;
                        }
                    });
                    console.log(newRoutes);
                    // newRoutes = changeRoutes(newRoutes)
                    reRenderTable(newRoutes)
                } else {
                    // createTable(routes)
                }
            }



        }

    }

</script>

<style>
    .app {
        width: 100%;
        display: flex;
        justify-content: center;
        flex-direction: column;
        align-items: center;
    }

    #table {
        max-width: 1140px;
        width: 100%;
        margin-top: 200px;
    }

    .search {
        max-width: 200px;
    }

    .marked {
        background-color: yellow;
    }

    tr {
        width: 100%;
    }

    td {
        width: 100%;
        border: 1px solid black;
    }

    th {
        cursor: pointer;
    }

    table {
        width: 100%;


    }
</style>

