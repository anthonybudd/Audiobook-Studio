<template>
    <DialogContent class="sm:max-w-md">
        <DialogHeader>
            <DialogTitle>Update Cover</DialogTitle>
            <DialogDescription>
                Update the cover image for {{ project.title }}.
            </DialogDescription>
        </DialogHeader>

        <Button
            variant="outline"
            @click="fileInput?.click()"
        >
            Select Image
        </Button>

        <input
            id="fileInput"
            ref="fileInput"
            accept="image/*"
            @change="handleFileChange($event)"
            type="file"
            class="hidden"
        />

        <DialogFooter>
            <DialogClose as-child>
                <Button variant="outline">
                    Cancel
                </Button>
            </DialogClose>
            <Button
                @click="updateCover"
                :disabled="!file"
            >
                Update Cover
            </Button>
        </DialogFooter>
    </DialogContent>
</template>

<script setup lang="ts">
import { DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogTitle, DialogClose } from '@/components/ui/dialog';
import type { Project } from '@/lib/types';
import { Button } from '@/components/ui/button';
import { injectDialogRootContext } from 'reka-ui';
import { useRouter } from 'vue-router';
import { computed, ref } from 'vue';
import { toast } from 'vue-sonner';
import { useStore } from 'vuex';

const store = useStore();
const dialog = injectDialogRootContext();

const props = defineProps<{
    project: Project;
}>();

const isLoading = ref(false);
const fileInput = ref<HTMLInputElement | null>(null);
const file = ref<File | null>(null);

function updateCover() {
    if (isLoading.value) return;
    isLoading.value = true;

    try {
        console.log(file.value);
        store.commit('updateProjectCover', { projectID: props.project.id, cover: file.value });
        toast.success('Cover updated');
        dialog.onOpenChange(false);

    } catch (error) {
        console.error(error);
        toast.error('Failed to update cover');
    } finally {
        isLoading.value = false;
    }
}

function handleFileChange(event: Event) {
    const input = event.target as HTMLInputElement;
    file.value = input.files?.[0] ?? null;
}
</script>
