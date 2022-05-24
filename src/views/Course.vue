<template>
    <div class="courses">
        <div class="hero is-info">
            <div class="hero-body has-text-centered">
                <h1 class="title">{{ course.title }}</h1>

                <router-link
                    :to="{name: 'Author', params: {id: course.created_by.id }}"
                    class="subtitle"
                >
                    By {{ course.created_by.first_name + ' ' + course.created_by.last_name }}
                </router-link>
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

                                <span class="tag is-warning" v-if="activity.status == 'started'" @click="markAsDone">Started (mark as done)</span>
                                <span class="tag is-success" v-else>Done</span>

                                <hr>
                                
                                {{ activeLesson.long_description }}

                                <hr>

                                <template v-if="activeLesson.lesson_type === 'quiz'">
                                    <Quiz
                                        v-bind:quiz="quiz"
                                    />
                                </template>

                                <template v-if="activeLesson.lesson_type === 'video'">
                                    <Video
                                        v-bind:youtube_id="activeLesson.youtube_id"
                                    />
                                </template>

                                <template  v-if="activeLesson.lesson_type === 'article'">
                                    <CourseComment
                                        v-for="comment in comments"
                                        v-bind:key="comment.id"
                                        v-bind:comment="comment"
                                    />

                                    <AddComment
                                        v-bind:course="course"
                                        v-bind:activeLesson="activeLesson"
                                        v-on:submitComment="submitComment"
                                    />
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

import CourseComment from '@/components/CourseComment'
import AddComment from '@/components/AddComment'
import Quiz from '@/components/Quiz'
import Video from '@/components/Video'

export default {
    components: {
        CourseComment,
        AddComment,
        Quiz,
        Video
    },
    data() {
        return {
            course: {
                created_by: {
                    id: 0
                }
            },
            lessons: [],
            comments: [],
            activeLesson: null,
            errors: [],
            quiz: {},
            activity: {}
        }
    },
    async mounted() {
        console.log('mounted')

        const slug = this.$route.params.slug

        await axios
            .get(`courses/${slug}/`)
            .then(response => {
                console.log(response.data)

                this.course = response.data.course
                this.lessons = response.data.lessons
            })
        
        document.title = this.course.title + ' | StudyNet'
    },
    methods: {
        submitComment(comment) {
            this.comments.push(comment)
        },
        setActiveLesson(lesson) {
            this.activeLesson = lesson

            if (lesson.lesson_type === 'quiz') {
                this.getQuiz()
            } else {
                this.getComments()
            }

            this.trackStarted()
        },
        trackStarted() {
            axios
                .post(`activities/track_started/${this.$route.params.slug}/${this.activeLesson.slug}/`)
                .then(response => {
                    console.log(response.data)

                    this.activity = response.data
                })
        },
        markAsDone() {
            axios
                .post(`activities/mark_as_done/${this.$route.params.slug}/${this.activeLesson.slug}/`)
                .then(response => {
                    console.log(response.data)

                    this.activity = response.data
                })
        },
        getQuiz() {
            axios
                .get(`courses/${this.course.slug}/${this.activeLesson.slug}/get-quiz/`)
                .then(response => {
                    console.log(response.data)

                    this.quiz = response.data
                })
        },
        getComments() {
            axios
                .get(`courses/${this.course.slug}/${this.activeLesson.slug}/get-comments/`)
                .then(response => {
                    console.log(response.data)

                    this.comments = response.data
                })
        }
    }
}
</script>