<script>
  export let convertResult;
  export let convertData;
  export let conversionRate;
</script>

<div class="card fluid" style="text-align:center">
  <div>{convertResult.amount} {convertResult.fromID} =</div>
  <div>
    <span style="font-size: 50px">{convertResult.convertedAmount}</span>
    <span> {convertResult.toID}</span>
  </div>
  <div>
    1 {convertData.toID} = {conversionRate.from}
    {convertData.fromID}
  </div>
  <div>
    1 {convertData.fromID} = {conversionRate.to}
    {convertData.toID}
  </div>
</div>

<style>
  .card {
    padding: 1rem;
  }
</style>
