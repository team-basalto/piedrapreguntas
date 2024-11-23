<script>
    import 'bootstrap-grid.css'
    import Button from '../../stories/Button.svelte';
    const {question_image,question_text,correct_answer,
        option_a,
        option_b,
        option_c,
        option_d
    } = $props()
</script>

<div class="container-fluid">
    <div class = "card">
        <img src= '${ question_image }' class="card-img-top" alt="...">
        <div class="card-body">
            <h2> 
                ${ question_text }
            </h2>
            <div class = "row">
                <Button size='medium' label = '${ option_a }'> </Button>
                <Button size='medium' label = '${ option_b }'> </Button>
            </div>
            <div class = "row">
                <Button size='medium' label = '${ option_c }'> </Button>
                <Button size='medium' label = '${ option_d }'> </Button>
            </div>
        </div>
    </div>
</div>