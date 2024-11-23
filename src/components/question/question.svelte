<script>
    import 'bootstrap-grid.css'
    import Button from '../../stories/Button.svelte';
    const {question_image,question_text,correct_answer} = $props()
</script>

<div class="container-fluid">
    <div class = "card">
        <img src= '${ question_image }' class="card-img-top" alt="...">
        <div class="card-body">
            <h2> 
                ${ question_text }
            </h2>
            <div class = "row">

            </div>
            <div class = "row">

            </div>
        </div>
    </div>
</div>