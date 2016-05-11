E10s Experiment Derived Streams
-------------------------------

To create a derived dataset for running analyses like
[e10s_experiment.ipynb](https://github.com/vitillo/e10s_analyses/tree/master/beta46-apz)
:

1. Fire up a 20-node Spark cluster [here](https://analysis.telemetry.mozilla.org/).
2. `cd /mnt && git clone https://github.com/mozilla/telemetry-batch-view.git && cd telemetry-batch-view`
3. Edit `src/main/scala/DerivedStream.scala` around `case "E10SExperiment"` to have a fromDate, name, and path reflecting your experiment
4. `sbt assembly` (this might take a while)
5. `spark-submit --master yarn-client --class telemetry.DerivedStream target/scala-2.10/telemetry-batch-view-*.jar --to-date YYYYMMDD E10SExperiment` (this _definitely_ will take a while. You can watch some of its progress by `tail -f /mnt/spark.log`)

Testing
-------

Before running on the full fromDate to to-date, specify a to-date of fromDate + 1.
Then edit `src/main/resources/application.conf` to point to `telemetry-test-bucket`
instead of `telemetry-parquet`.

To verify that data was generated by your test run, you can use the `aws` utility
`aws s3 ls s3://telemetry-test-bucket/e10s_experiment/<your experiment name with '-' replaced with '_'>/v<to-date as YYYYMMDD>/`

e.g. `aws s3 ls s3://telemetry-test-bucket/e10s_experiment/e10s_beta47_cohorts/v20160427/`

If it shows you a list of GUIDs, then it's generating data.

Remember to change back to `telemetry-parquet` before regenerating using the complete date range.
