<script setup lang="ts">
import InputError from '@/components/InputError.vue';
import TextLink from '@/components/TextLink.vue';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import AuthBase from '@/layouts/AuthLayout.vue';
import { Head, useForm } from '@inertiajs/vue3';
import { LoaderCircle } from 'lucide-vue-next';

const form = useForm({
    name: '',
    cpf: '',
    birthdate: '',
    email: '',
    password: '',
    password_confirmation: '',
});

const submit = () => {
    form.post(route('register'), {
        onFinish: () => form.reset('password', 'password_confirmation'),
    });
};

const formatarCPF = () => {
    let valor = form.cpf.replace(/\D/g, '');
    valor = valor.substring(0, 11);

    if (valor.length > 9) {
        valor = valor.replace(/(\d{3})(\d{3})(\d{3})(\d+)/, '$1.$2.$3-$4');
    } else if (valor.length > 6) {
        valor = valor.replace(/(\d{3})(\d{3})(\d+)/, '$1.$2.$3');
    } else if (valor.length > 3) {
        valor = valor.replace(/(\d{3})(\d+)/, '$1.$2');
    }

    form.cpf = valor;
};

const today = new Date();

const minBirthdate = new Date(today);
minBirthdate.setUTCFullYear(today.getUTCFullYear() - 90);
const maxBirthdate = new Date(today);
maxBirthdate.setUTCFullYear(today.getUTCFullYear() - 12);
</script>

<template>
    <AuthBase title="Crie uma conta" description="Preencha as informações abaixo para criar sua conta">

        <Head title="Cadastrar-se" />

        <form @submit.prevent="submit" class="flex flex-col gap-6">
            <div class="grid gap-6 grid-cols-2">
                <div class="grid gap-2 col-span-full">
                    <Label for="name">Nome completo</Label>
                    <Input id="name" type="text" required autofocus :tabindex="1" autocomplete="name" v-model="form.name" placeholder="Nome completo" />
                    <InputError :message="form.errors.name" />
                </div>

                <div class="grid gap-2">
                    <Label for="cpf">CPF</Label>
                    <Input id="cpf" type="tel" required :tabindex="2" autocomplete="cpf" v-model="form.cpf" placeholder="CPF" @input="formatarCPF" />
                    <InputError :message="form.errors.cpf" />
                </div>

                <div class="grid gap-2">
                    <Label for="birthdate">Data de nascimento</Label>
                    <Input id="birthdate" type="date" required :tabindex="3" autocomplete="bday" v-model="form.birthdate"  :min="minBirthdate.toISOString().slice(0, 10)" :max="maxBirthdate.toISOString().slice(0, 10)" />
                    <InputError :message="form.errors.birthdate" />
                </div>

                <div class="grid gap-2 col-span-full">
                    <Label for="email">E-mail</Label>
                    <Input id="email" type="email" required :tabindex="4" autocomplete="email" v-model="form.email" placeholder="email@exemplo.com" />
                    <InputError :message="form.errors.email" />
                </div>

                <div class="grid gap-2">
                    <Label for="password">Senha</Label>
                    <Input
                        id="password"
                        type="password"
                        required
                        :tabindex="5"
                        autocomplete="new-password"
                        v-model="form.password"
                        placeholder="Senha" />
                    <InputError :message="form.errors.password" />
                </div>

                <div class="grid gap-2">
                    <Label for="password_confirmation">Confirmação de senha</Label>
                    <Input
                        id="password_confirmation"
                        type="password"
                        required
                        :tabindex="6"
                        autocomplete="new-password"
                        v-model="form.password_confirmation"
                        placeholder="Confirmação de senha" />
                    <InputError :message="form.errors.password_confirmation" />
                </div>

                <Button type="submit" class="mt-2 w-full col-span-full" tabindex="7" :disabled="form.processing">
                    <LoaderCircle v-if="form.processing" class="h-4 w-4 animate-spin" />
                    Cadastrar-se
                </Button>
            </div>

            <div class="text-center text-sm text-muted-foreground">
                Já possui uma conta?
                <TextLink :href="route('login')" class="underline underline-offset-4" :tabindex="8">Entrar</TextLink>
            </div>
        </form>
    </AuthBase>
</template>
