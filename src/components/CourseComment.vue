<template>
    <article class="media box">
        <div class="media-content">
            <div class="content">
                <p>
                    <strong>{{ comment.name }}</strong> {{ comment.created_at }}<br>
                    {{ comment.content }}
                </p>
            </div>
        </div>
    </article>
</template>

<script>
export default {
    props: ['comment']
}
</script>