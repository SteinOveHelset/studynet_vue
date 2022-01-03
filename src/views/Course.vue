<template>
    <div class="courses">
        <div class="hero is-info">
            <div class="hero-body has-text-centered">
                <h1 class="title">{{ course.title }}</h1>
            </div>
        </div>

        <section class="section">
            <div class="container">
                <div class="columns content">
                    <div class="column is-2">
                        <h2>Table of contents</h2>

                        <ul>
                            <li
                                v-for="lesson in lessons"
                                v-bind:key="lesson.id"
                            >
                                <a @click="setActiveLesson(lesson)">{{ lesson.title }}</a>
                            </li>
                        </ul>
                    </div>

                    <div class="column is-10">
                        <template v-if="$store.state.user.isAuthenticated">
                            <template v-if="activeLesson">
                                <h2>{{ activeLesson.title }}</h2>
                                
                                {{ activeLesson.long_description }}

                                <hr>

                                <template v-if="activeLesson.lesson_type === 'quiz'">
                                    <h3>{{ quiz.question }}</h3>

                                    <div class="control">
                                        <label class="radio">
                                            <input type="radio" :value="quiz.op1" v-model="selectedAnswer"> {{ quiz.op1 }}
                                        </label>
                                    </div>

                                    <div class="control">
                                        <label class="radio">
                                            <input type="radio" :value="quiz.op2" v-model="selectedAnswer"> {{ quiz.op2 }}
                                        </label>
                                    </div>

                                    <div class="control">
                                        <label class="radio">
                                            <input type="radio" :value="quiz.op3" v-model="selectedAnswer"> {{ quiz.op3 }}
                                        </label>
                                    </div>

                                    <div class="control mt-4">
                                        <button class="button is-info" @click="submitQuiz">Submit</button>
                                    </div>

                                    <template v-if="quizResult == 'correct'">
                                        <div class="notification is-success mt-4">Correct :-D</div>
                                    </template>

                                    <template v-if="quizResult == 'incorrect'">
                                        <div class="notification is-danger mt-4">Wrong :-( Please try again!</div>
                                    </template>
                                </template>

                                <template  v-if="activeLesson.lesson_type === 'article'">
                                    <article 
                                        class="media box"
                                        v-for="comment in comments"
                                        v-bind:key="comment.id"
                                    >
                                        <div class="media-content">
                                            <div class="content">
                                                <p>
                                                    <strong>{{ comment.name }}</strong> {{ comment.created_at }}<br>
                                                    {{ comment.content }}
                                                </p>
                                            </div>
                                        </div>
                                    </article>

                                    <form v-on:submit.prevent="submitComment()">
                                        <div class="field">
                                            <label class="label">Name</label>
                                            <div class="control">
                                                <input type="text" class="input" v-model="comment.name">
                                            </div>
                                        </div>

                                        <div class="field">
                                            <label class="label">Content</label>
                                            <div class="control">
                                                <textarea class="textarea" v-model="comment.content"></textarea>
                                            </div>
                                        </div>

                                        <div 
                                            class="notification is-danger"
                                            v-for="error in errors"
                                            v-bind:key="error"
                                        >
                                            {{ error }}
                                        </div>

                                        <div class="field">
                                            <div class="control">
                                                <button class="button is-link">Submit</button>
                                            </div>
                                        </div>
                                    </form>
                                </template>
                            </template>

                            <template v-else>
                                {{ course.long_description }}
                            </template>
                        </template>

                        <template v-else>
                            <h2>Restricted access</h2>
                            
                            <p>You need to have an account to continue!</p>
                        </template>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            course: {},
            lessons: [],
            comments: [],
            activeLesson: null,
            errors: [],
            quiz: {},
            selectedAnswer: '',
            quizResult: null,
            comment: {
                name: '',
                content: ''
            }
        }
    },
    async mounted() {
        console.log('mounted')

        const slug = this.$route.params.slug

        await axios
            .get(`/api/v1/courses/${slug}/`)
            .then(response => {
                console.log(response.data)

                this.course = response.data.course
                this.lessons = response.data.lessons
            })
        
        document.title = this.course.title + ' | StudyNet'
    },
    methods: {
        submitQuiz() {
            this.quizResult = null

            if (this.selectedAnswer) {
                if (this.selectedAnswer === this.quiz.answer) {
                    this.quizResult = 'correct'
                } else {
                    this.quizResult = 'incorrect'
                }
            } else {
                alert('Select answer first')
            }
        },
        submitComment() {
            console.log('submitComment')

            this.errors = []

            if (this.comment.name === '') {
                this.errors.push('The name must be filled out')
            }

            if (this.comment.content === '') {
                this.errors.push('The content must be filled out')
            }

            if (!this.errors.length) {
                axios
                    .post(`/api/v1/courses/${this.course.slug}/${this.activeLesson.slug}/`, this.comment)
                    .then(response => {
                        this.comment.name = ''
                        this.comment.content = ''

                        this.comments.push(response.data)
                    })
                    .catch(error => {
                        console.log(error)
                    })
            }
        },
        setActiveLesson(lesson) {
            this.activeLesson = lesson

            if (lesson.lesson_type === 'quiz') {
                this.getQuiz()
            } else {
                this.getComments()
            }
        },
        getQuiz() {
            axios
                .get(`/api/v1/courses/${this.course.slug}/${this.activeLesson.slug}/get-quiz/`)
                .then(response => {
                    console.log(response.data)

                    this.quiz = response.data
                })
        },
        getComments() {
            axios
                .get(`/api/v1/courses/${this.course.slug}/${this.activeLesson.slug}/get-comments/`)
                .then(response => {
                    console.log(response.data)

                    this.comments = response.data
                })
        }
    }
}
</script>