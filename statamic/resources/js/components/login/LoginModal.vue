<template>

    <div>

        <modal :show="show" class="modal-login" :shake="hasErrors">
            <template slot="header">
                {{ translate('cp.login_to_continue') }}
            </template>
            <template slot="body">
                <div class="mb-2">
                    <label :class="{ 'text-red': errors.password.length }">{{ translate('cp.password_for', { username: this.username }) }} <i class="required">*</i></label>
                    <input type="password" name="password" class="form-control" v-model="password" v-el:password @keydown.enter.prevent="submit" />
                    <small class="block text-red mt-1" v-if="errors.username.length">{{ errors.username[0] }}</small>
                    <small class="block text-red mt-1" v-if="errors.password.length">{{ errors.password[0] }}</small>
                </div>
            </template>
            <template slot="footer">
                <button @click.prevent="submit" class="btn btn-primary">{{ translate('cp.submit') }}</button>
            </template>
        </modal>

    </div>

</template>

<script>
export default {

    props: ['username'],

    data() {
        return {
            show: true,
            errors: [],
            password: null
        }
    },

    ready() {
        this.$http.get(cp_url('auth/token')).success(response => {
            Vue.http.headers.common['X-CSRF-TOKEN'] = response;
        });

        this.$els.password.focus();
    },

    computed: {
        hasErrors() {
            return ! _.isEmpty(this.errors);
        }
    },

    methods: {

        submit() {
            this.errors = []; // reset errors
            this.$http.post(cp_url('auth/login'), {
                username: this.username,
                password: this.password
            }).success(response => {
                this.errors = [];
                this.$notify.success(translate('cp.logged_in'));
                this.show = false;
                this.$emit('closed');
            }).error(response => {
                this.errors = response;
            });
        }

    }

}
</script>
