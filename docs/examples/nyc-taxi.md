#NYC Taxi Dataset
In this example, we will use the NYC Taxi dataset to demonstrate how to use Epsio to ....
## Prerequisites
- An empty PostgreSQL instance
- An Epsio instance connected to the PostgreSQL instance
<hr>
## **Steps**
### **1. Simulate Workload**
In this Example, 

To simulate a "live" deployment, we will use a script that "replays" the NYC data .
First, we download the script to simulate
```bash
$ curl -O https://epsio.io/examples/nyc-taxi/simulate.py
```
Then, run the script to simulate the data
```bash
$ python simulate.py <host> <username> <password>
```

### 2. Explore the dataset
The NYC dataset contains 3 tables, `trips`, `rates` and `nyc_zones`.
```psql
List of relations
 Schema |     Name      | Type  |  Owner
--------+---------------+-------+----------
 public | nyc_zones     | table | postgres
 public | rates         | table | postgres
 public | trips         | table | postgres
(3 rows)
```

The `rides` table contains the actual rides data, and has the following schema:
```psql
                             Table "public.trips"
        Column         |            Type             | Collation | Nullable | Default 
-----------------------+-----------------------------+-----------+----------+---------
 id                    | integer                     |           |          | 
 vendorid              | integer                     |           |          | 
 tpep_pickup_datetime  | timestamp without time zone |           |          | 
 tpep_dropoff_datetime | timestamp without time zone |           |          | 
 passenger_count       | numeric(16,2)               |           |          | 
 trip_distance         | numeric(16,2)               |           |          | 
 ratecodeid            | numeric(16,2)               |           |          | 
 store_and_fwd_flag    | character(1)                |           |          | 
 pulocationid          | integer                     |           |          | 
 dolocationid          | integer                     |           |          | 
 payment_type          | integer                     |           |          | 
 fare_amount           | numeric(16,2)               |           |          | 
 extra                 | numeric(16,2)               |           |          | 
 mta_tax               | numeric(16,2)               |           |          | 
 tip_amount            | numeric(16,2)               |           |          | 
 tolls_amount          | numeric(16,2)               |           |          | 
 improvement_surcharge | numeric(16,2)               |           |          | 
 total_amount          | numeric(16,2)               |           |          | 
 congestion_surcharge  | numeric(16,2)               |           |          | 
 airport_fee           | numeric(16,2)               |           |          | 
```


Since our background script keeps inserting new data, the trips table contantly gets new data.

Whenever you run the following query, the amount of rows will increase:
<div class="run-box-playground">
    <div class="runbox-code highlight">
        <span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="n">COUNT</span><span class="p">(</span><span class="o">*</span><span class="p">)</span><span class="w"> </span><span class="k">FROM</span><span class="w"> </span><span class="n">trips</span><span class="p">;</span>
</span>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
        Results:
        <br>
        <div class="runbox-results-area"></div>
    </div>
</div>

### 3. Excelerate Queries??? Use Epsio For queries??? What would be a good title?
Now that we have data in our database, we can start querying it.

####3.1 What is the average trip distance?

To find out the average trip distance, we can run the following query:
<div class="run-box-playground">
    <div class="runbox-code highlight">
<span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="n">AVG</span><span class="p">(</span><span class="n">trip_distance</span><span class="p">)</span><span class="w"> </span><span class="k">FROM</span><span class="w"> </span><span class="n">trips</span><span class="p">;</span>
</span>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
        Results:
        <br>
        <div class="runbox-results-area"></div>
    </div>
</div>

Since this query takes long, let's create a view 

<br>
<div class='exmaple-wrapper'>
<h3>Using Epsio to maintain average trip length</h3>
First, we create a view for the average trip distance:<br>
<div class="run-box-playground">
    <div class="runbox-code highlight">
    <span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">CALL</span><span class="w"> </span><span class="n">epsio</span><span class="mf">.</span><span class="n">create_view</span><span class="p">(</span><span class="s1">'avg_trip_distance'</span><span class="p">,</span><span class="w"> </span><span class="s1">'SELECT AVG(trip_distance) FROM trips'</span><span class="p">);</span>
    </span>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
        Results:
        <br>
        <div class="runbox-results-area"></div>
    </div>
</div>
<br>
Then, to fetch the results of the view, we can run the following query (results are now super fast):
<div class="run-box-playground">
    <div class="runbox-code highlight">
    <code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="o">*</span><span class="w"> </span><span class="k">FROM</span><span class="w"> </span><span class="n">avg_trip_distance</span><span class="p">;</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
        Results:
        <br>
        <div class="runbox-results-area"></div>
    </div>
</div>
</div>
####3.2 How many rides of each rate type were taken?
To find out how many rides of each rate type were taken, we can run the following query:
<div class="run-box-playground">
    <div class="runbox-code highlight">
        <code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="n">rates</span><span class="mf">.</span><span class="n">description</span><span class="p">,</span><span class="w"> </span><span class="n">COUNT</span><span class="p">(</span><span class="o">*</span><span class="p">)</span><span class="w"> </span><span class="k">AS</span><span class="w"> </span><span class="n">num_trips</span>
</span><span id="__span-4-2"><a id="__codelineno-4-2" name="__codelineno-4-2" href="#__codelineno-4-2"></a><span class="k">FROM</span><span class="w"> </span><span class="n">trips</span>
</span><span id="__span-4-3"><a id="__codelineno-4-3" name="__codelineno-4-3" href="#__codelineno-4-3"></a><span class="k">JOIN</span><span class="w"> </span><span class="n">rates</span><span class="w"> </span><span class="k">ON</span><span class="w"> </span><span class="n">trips</span><span class="mf">.</span><span class="n">ratecodeid</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="n">rates</span><span class="mf">.</span><span class="n">rate_code</span>
</span><span id="__span-4-4"><a id="__codelineno-4-4" name="__codelineno-4-4" href="#__codelineno-4-4"></a><span class="k">GROUP</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">rates</span><span class="mf">.</span><span class="n">description</span>
</span><span id="__span-4-5"><a id="__codelineno-4-5" name="__codelineno-4-5" href="#__codelineno-4-5"></a><span class="k">ORDER</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">LOWER</span><span class="p">(</span><span class="n">rates</span><span class="mf">.</span><span class="n">description</span><span class="p">);</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>
<br>
<div class='exmaple-wrapper'>
<h3>Using Epsio to maintain rides count per rate type</h3>

Create View
<div class="run-box-playground">
    <div class="runbox-code highlight">
<code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">CALL</span><span class="w"> </span><span class="n">epsio</span><span class="mf">.</span><span class="n">create_view</span><span class="p">(</span><span class="s1">'rides_per_rate'</span><span class="p">,</span>
</span><span id="__span-4-2"><a id="__codelineno-4-2" name="__codelineno-4-2" href="#__codelineno-4-2"></a><span class="w">   </span><span class="s1">'SELECT rates.description, COUNT(*) AS num_trips</span>
</span><span id="__span-4-3"><a id="__codelineno-4-3" name="__codelineno-4-3" href="#__codelineno-4-3"></a><span class="s1">    FROM trips</span>
</span><span id="__span-4-4"><a id="__codelineno-4-4" name="__codelineno-4-4" href="#__codelineno-4-4"></a><span class="s1">    JOIN rates ON trips.ratecodeid = rates.rate_code</span>
</span><span id="__span-4-5"><a id="__codelineno-4-5" name="__codelineno-4-5" href="#__codelineno-4-5"></a><span class="s1">    GROUP BY rates.description'</span><span class="p">);</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>

Query The View (say again something about fast results now...)
<div class="run-box-playground">
    <div class="runbox-code highlight">
<code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="o">*</span><span class="w"> </span><span class="k">FROM</span><span class="w"> </span><span class="n">rides_per_rate</span><span class="w"> </span><span class="k">ORDER</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">LOWER</span><span class="p">(</span><span class="n">description</span><span class="p">);</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>

</div>


####3.3 What are the top 10 zones where the most taxi rides originate from?


To find out the top 10 zones where the most taxi rides originate from, we can run the following query:
<div class="run-box-playground">
    <div class="runbox-code highlight">
        <code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="n">nz</span><span class="mf">.</span><span class="k">zone</span><span class="p">,</span><span class="w"> </span><span class="n">COUNT</span><span class="p">(</span><span class="o">*</span><span class="p">)</span><span class="w"> </span><span class="k">AS</span><span class="w"> </span><span class="n">ride_count</span>
</span><span id="__span-4-2"><a id="__codelineno-4-2" name="__codelineno-4-2" href="#__codelineno-4-2"></a><span class="k">FROM</span><span class="w"> </span><span class="n">trips</span><span class="w"> </span><span class="k">AS</span><span class="w"> </span><span class="n">t</span>
</span><span id="__span-4-3"><a id="__codelineno-4-3" name="__codelineno-4-3" href="#__codelineno-4-3"></a><span class="k">JOIN</span><span class="w"> </span><span class="n">nyc_zones</span><span class="w"> </span><span class="k">AS</span><span class="w"> </span><span class="n">nz</span><span class="w"> </span><span class="k">ON</span><span class="w"> </span><span class="n">t</span><span class="mf">.</span><span class="n">pulocationid</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="n">nz</span><span class="mf">.</span><span class="n">locationid</span>
</span><span id="__span-4-4"><a id="__codelineno-4-4" name="__codelineno-4-4" href="#__codelineno-4-4"></a><span class="k">GROUP</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">nz</span><span class="mf">.</span><span class="k">zone</span>
</span><span id="__span-4-5"><a id="__codelineno-4-5" name="__codelineno-4-5" href="#__codelineno-4-5"></a><span class="k">ORDER</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">ride_count</span><span class="w"> </span><span class="k">DESC</span><span class="w"> </span><span class="k">LIMIT</span><span class="w"> </span><span class="mf">10</span><span class="p">;</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>
<br>
<div class='exmaple-wrapper'>
<h3>Using Epsio to maintain rides count per rate type</h3>

Create View
<div class="run-box-playground">
    <div class="runbox-code highlight">
<code><span id="__span-5-1"><a id="__codelineno-5-1" name="__codelineno-5-1" href="#__codelineno-5-1"></a><span class="k">CALL</span><span class="w"> </span><span class="n">epsio</span><span class="mf">.</span><span class="n">create_view</span><span class="p">(</span><span class="s1">'top_10_zones'</span><span class="p">,</span>
</span><span id="__span-5-2"><a id="__codelineno-5-2" name="__codelineno-5-2" href="#__codelineno-5-2"></a><span class="w">   </span><span class="s1">'SELECT nz.zone, COUNT(*) AS ride_count</span>
</span><span id="__span-5-3"><a id="__codelineno-5-3" name="__codelineno-5-3" href="#__codelineno-5-3"></a><span class="s1">    FROM trips AS t</span>
</span><span id="__span-5-4"><a id="__codelineno-5-4" name="__codelineno-5-4" href="#__codelineno-5-4"></a><span class="s1">    JOIN nyc_zones AS nz ON t.pulocationid = nz.locationid</span>
</span><span id="__span-5-5"><a id="__codelineno-5-5" name="__codelineno-5-5" href="#__codelineno-5-5"></a><span class="s1">    GROUP BY nz.zone</span>
</span><span id="__span-5-6"><a id="__codelineno-5-6" name="__codelineno-5-6" href="#__codelineno-5-6"></a><span class="s1">    ORDER BY ride_count DESC LIMIT 10'</span><span class="p">);</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>

Query The View (say again something about fast results now...)
<div class="run-box-playground">
    <div class="runbox-code highlight">
    <code><span id="__span-4-1"><a id="__codelineno-4-1" name="__codelineno-4-1" href="#__codelineno-4-1"></a><span class="k">SELECT</span><span class="w"> </span><span class="o">*</span><span class="w"> </span><span class="k">FROM</span><span class="w"> </span><span class="n">top_10_zones</span><span class="w"> </span><span class="k">ORDER</span><span class="w"> </span><span class="k">BY</span><span class="w"> </span><span class="n">ride_count</span><span class="w"> </span><span class="k">DESC</span><span class="p">;</span>
</span></code>
    </div>
    <span class="md-button md-button--primary run-button-in-runbox">Run &gt;</span>
    <div class="runbox-results">
Results:
<div class="runbox-results-area"></div></div>
</div>

</div>


<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>

<script type="text/javascript">
// This function will be called when the span is clicked
function myFunction(event) {
    clickedElement = event.target;
    //clickedElement.parentElement.querySelector('.runbox-results').style.display = 'none';
    clickedElement.innerHTML= '<i class="fa fa-circle-o-notch fa-spin"></i>';
    $.ajax({
                url: 'https://nyc-taxis-demo.epsio.io/',
                type: 'POST',
                data: {
                    query: clickedElement.parentElement.querySelector(".runbox-code").innerText,
                    uuid: client_uuid
                },
                success: function(response) {
                    asdf = response;
                    if ('error' in response)
                    {
                        res = response['error'].replaceAll("\\n", "<br>");
                        res = res.replaceAll("b'Timing is on.", "");
                        res = res.slice(0,-1);

                        clickedElement.parentElement.querySelector(".runbox-results-area").innerHTML = res;
                        clickedElement.parentElement.querySelector('.runbox-results').style.display = 'flow';

                    }
                    else {
                        res = response['query_result'].replaceAll("\\n", "<br>");
                        res = res.replaceAll("b'Timing is on.", "");
                        res = res.slice(0,-1);
                        clickedElement.parentElement.querySelector(".runbox-results-area").innerHTML = res;
                        clickedElement.parentElement.querySelector('.runbox-results').style.display = 'flow';
                    }
                },
                error: function(error) {
                    console.log("An error occurred:", error);
                },
                complete: function() {
                    clickedElement.innerHTML= 'Run >';
                }
            });
}

// Add an event listener to the span once the page has fully loaded
window.onload = function() {
    client_uuid = crypto.randomUUID();
    spanElements = document.getElementsByClassName('run-button-in-runbox');
    for (let i = 0; i < spanElements.length; i++) {
        spanElements[i].addEventListener('click', myFunction);
    }
}
</script>