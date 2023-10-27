<script setup>
import ChatContent from "@/Components/ChatContent.vue";
import ChatLayout from "./ChatLayout.vue";
import { Link, useForm } from "@inertiajs/vue3";
import { onMounted, ref } from "vue";

const promptInput = ref(null);
const chatContainer = ref(null);

const props = defineProps({
    messages: Array,
    chat: null | Object
});

const form = useForm({
    prompt: "",
});

const submit = () => {
    const url = props.chat ? `/chat/${props.chat?.id}` : '/chat';
    form.post(url, {
        onFinish: () => clear()
    });
};

const scrollToBottom = () => {
    const el = chatContainer.value;
    el.scrollTop = el.scrollHeight;
}

const clear = () => {
    form.prompt = "";
    promptInput.value.focus();
    scrollToBottom();
}

onMounted(() => {
    clear();
})
</script>

<template>
    <ChatLayout>
        <template #aside>
            <ul class="p-2">
                <li v-if="chat"
                        class="px-4 py-2 my-2 flex justify-between font-semibold text-green-400 bg-slate-900 hover:bg-slate-700 rounded-lg duration-200">
                        <Link href="/chat">New Chat</Link>
                    </li>
                <template v-for="message in messages" :key="message.id">
                    <li
                        class="px-4 py-2 my-2 flex justify-between font-semibold text-slate-400 bg-slate-900 hover:bg-slate-700 rounded-lg duration-200">
                        <Link :href="`/chat/${message.id}`">{{ message.context[0].content }}</Link>
                    </li>
                </template>
            </ul>
        </template>
        <div class="w-full flex text-white">
            <template v-if="chat">
                <div class="w-full flex h-screen bg-slate-900 pt-6">
                    <div class="w-full overflow-auto pb-36" ref="chatContainer">
                        <template v-for="(content, index) in chat?.context" :key="index">
                            <ChatContent :content="content"></ChatContent>
                        </template>
                    </div>
                </div>
            </template>
        </div>
        <template #form>
            <div class="w-full text-white flex">
                <section class="w-full px-6 top-0">
                    <div class="w-full">
                        <div class="relative flex-1 flex items-center ">
                            <input type="text" class="w-full bg-slate-700 text-white rounded-lg"
                                placeholder="Ask Laravel AI" v-model="form.prompt" @keyup.enter="submit"
                                :disabled="form.processing" ref="promptInput">
                            <div v-if="form.processing" class="dot-typing ml-8"></div>
                            <div class="absolute inset-y-0 right-0 flex items-center pl-3">
                                <svg v-if="!form - processing" xmlns="http://www.w3.org/2000/svg" fill="none"
                                    viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
                                    class="w-6 h-6 -ml-8 text-slate-200 cursor-pointer">
                                    <path stroke-linecap="round" stroke-linejoin="round"
                                        d="M6 12L3.269 3.126A59.768 59.768 0 0121.485 12 59.77 59.77 0 013.27 20.876L5.999 12zm0 0h7.5" />
                                </svg>
                            </div>
                        </div>
                    </div>
                </section>
            </div>
        </template>
    </ChatLayout>
</template>

<style>
.dot-typing {
    position: absolute;
    left: -9999px;
    width: 10px;
    height: 10px;
    border-radius: 5px;
    background-color: #9880ff;
    color: #9880ff;
    box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
        10014px 0 0 0 #9880ff;
    animation: dot-typing 1.5s infinite linear;
}

@keyframes dot-typing {
    0% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }

    16.667% {
        box-shadow: 9984px -10px 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }

    33.333% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }

    50% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px -10px 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }

    66.667% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }

    83.333% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px -10px 0 0 #9880ff;
    }

    100% {
        box-shadow: 9984px 0 0 0 #9880ff, 9999px 0 0 0 #9880ff,
            10014px 0 0 0 #9880ff;
    }
}</style>
