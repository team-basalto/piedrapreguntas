<script module>
    import { defineMeta } from '@storybook/addon-svelte-csf';
    import Question  from '../components/question/question.svelte';
    import { fn } from '@storybook/test';

    const { Story } = defineMeta({
    title: 'Example/Question',
    component: Question,
    tags: ['autodocs'],
    argTypes: {
    },
    args: {
    }
  });
</script>