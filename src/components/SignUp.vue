<template>
    <div>
        <div class="container">
            <spinner :loading="spinner.loading"></spinner>

            <form class="form-horizontal" role="form" v-on:submit.prevent="registerUser">
                <h2>Registration Form</h2>
                <div class="form-group">
                    <label for="firstName" class="col-sm-3 control-label">Full Name</label>
                    <div class="col-sm-9">
                        <input type="text" v-model="signUp.fullName" id="firstName" placeholder="Full Name"
                               class="form-control" autofocus>
                        <span class="help-block">Last Name, First Name, eg.: Smith, Harry</span>
                    </div>
                </div>
                <div class="form-group">
                    <label for="email" class="col-sm-3 control-label">Email</label>
                    <div class="col-sm-9">
                        <input type="email" id="email" v-model="signUp.email" placeholder="Email" class="form-control">
                    </div>
                </div>
                <div class="form-group">
                    <label for="password" class="col-sm-3 control-label">Password</label>
                    <div class="col-sm-9">
                        <input type="password" id="password" v-model="signUp.password" placeholder="Password"
                               class="form-control">
                    </div>
                </div>
                <div class="form-group">
                    <label for="rePassword" class="col-sm-3 control-label">Re Password</label>
                    <div class="col-sm-9">
                        <input type="password" id="rePassword" v-model="signUp.rePassword" placeholder="Password"
                               class="form-control">
                    </div>
                </div>
                <div class="form-group">
                    <div class="col-sm-9 col-sm-offset-3">
                        <div class="checkbox">
                            <label>
                                <input type="checkbox">I accept <a href="#">terms</a>
                            </label>
                        </div>
                    </div>
                </div> <!-- /.form-group -->
                <div class="form-group">
                    <div class="col-sm-9 col-sm-offset-3">
                        <button type="submit" class="btn btn-primary btn-block">Register</button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>
<script>
    import Spinner from './util/Spinner.vue'
    import VModal from 'vue-js-modal';

    const emailRE = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;

    export default {

        components: {
            'spinner': Spinner
        },

        data() {
            return {
                signUp: {
                    email: '',
                    password: '',
                    rePassword: '',
                    fullName: ''
                },
                spinner: {
                    loading: false,
                },
            }
        },
        methods: {
            registerUser: function () {
                if (this.validation.email && this.validation.password) {
                    this.spinner.loading = true;
                    this.$fireAuth.createUserWithEmailAndPassword(this.validation.emailSelf, this.validation.password)
                        .then((user) => {
                            Promise.all([
                                user.updateProfile({
                                    displayName: '' + this.signUp.fullName,
                                    photoURL: ''
                                }),
                                user.sendEmailVerification()]
                            ).then(() => {
                                this.$fireAuth.signOut();
                                this.spinner.loading = false;
                                this.$modal.show('dialog', {
                                    text: 'Please verify your email.Email was sent.'
                                });
                            })
                        }).catch(error => {
                        console.log(error);
                        this.spinner.loading = false;
                    })
                }
            },
        },
        computed: {
            validation: function () {
                return {
                    email: emailRE.test(this.signUp.email),
                    password: this.signUp.password,
                    emailSelf: this.signUp.email
                }
            },
        },
    }

</script>
<style>
    *[role="form"] {
        max-width: 530px;
        padding: 15px;
        margin: 0 auto;
        /*background-color: #fff;*/
        border-radius: 0.3em;
    }

    *[role="form"] h2 {
        margin-left: 5em;
        margin-bottom: 1em;
    }
</style>