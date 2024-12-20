<template>
    <GuestLayout>
        <Head title="Log in" />

        <div
            v-if="status"
            class="mb-4 rounded-lg bg-green-100 p-4 text-sm font-medium text-green-800 shadow-md"
        >
            {{ status }}
        </div>

        <form
            @submit.prevent="submit"
            class="mx-auto max-w-md rounded-lg bg-gradient-to-b from-gray-200 to-gray-300 p-8 shadow-xl"
        >
            <h1 class="mb-6 text-center text-3xl font-extrabold text-gray-900">
                Welcome Back
            </h1>

            <div class="mb-5">
                <InputLabel for="email" value="Email" class="text-gray-700 font-medium" />

                <TextInput
                    id="email"
                    type="email"
                    class="mt-2 block w-full rounded-lg border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 text-gray-900 placeholder-gray-400"
                    v-model="form.email"
                    required
                    autofocus
                    autocomplete="username"
                    placeholder="Enter your email"
                />

                <InputError class="mt-2 text-sm text-red-600" :message="form.errors.email" />
            </div>

            <div class="mb-5">
                <InputLabel for="password" value="Password" class="text-gray-700 font-medium" />

                <TextInput
                    id="password"
                    type="password"
                    class="mt-2 block w-full rounded-lg border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 text-gray-900 placeholder-gray-400"
                    v-model="form.password"
                    required
                    autocomplete="current-password"
                    placeholder="Enter your password"
                />

                <InputError class="mt-2 text-sm text-red-600" :message="form.errors.password" />
            </div>

            <div class="mb-6">
                <label class="flex items-center text-gray-700">
                    <Checkbox
                        name="remember"
                        v-model:checked="form.remember"
                        class="rounded border-gray-300 text-indigo-600 shadow-sm focus:ring-indigo-500"
                    />
                    <span class="ml-2 text-sm font-medium">Remember me</span>
                </label>
            </div>

            <div class="flex items-center justify-between">
                <Link
                    v-if="canResetPassword"
                    :href="route('password.request')"
                    class="text-sm text-indigo-600 hover:text-indigo-800 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2"
                >
                    Forgot your password?
                </Link>

                <PrimaryButton
                    class="ml-4 rounded-lg bg-gradient-to-r from-purple-800 to-purple-900 px-6 py-2 text-white font-bold shadow-md hover:from-purple-700 hover:to-purple-800 focus:outline-none focus:ring-4 focus:ring-indigo-300 disabled:opacity-50 transform transition-transform duration-200"
                    :class="{ 'opacity-25': form.processing }"
                    :disabled="form.processing"
                >
                    Log in
                </PrimaryButton>
            </div>
        </form>
    </GuestLayout>
</template>

<script setup>
import Checkbox from '@/Components/Checkbox.vue';
import GuestLayout from '@/Layouts/GuestLayout.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';

defineProps({
    canResetPassword: {
        type: Boolean,
    },
    status: {
        type: String,
    },
});

const form = useForm({
    email: '',
    password: '',
    remember: false,
});

const submit = () => {
    form.post(route('login'), {
        onFinish: () => form.reset('password'),
    });
};
</script>

<style scoped>
form {
    border-radius: 12px;
    box-shadow: 0px 6px 16px rgba(0, 0, 0, 0.1);
    padding: 2.5rem;
    background: linear-gradient(to bottom, #f1f5f9, #e2e8f0); /* Grayish background */
}

h1 {
    font-family: 'Inter', sans-serif;
    letter-spacing: 0.05em;
    color: #1a202c;
}

input::placeholder {
    color: #9ca3af;
}

button {
    font-family: 'Inter', sans-serif;
    transition: background-color 0.3s, transform 0.2s;
}

button:hover {
    transform: scale(1.05);
}

button:disabled {
    cursor: not-allowed;
}
</style>
