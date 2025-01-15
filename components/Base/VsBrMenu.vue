<template>
    <div>
        <div class="vs-sticky-nav" :class="{ 'has-edit-button': page.isPreview() }">
            <VsBrSkipTo />
            <header>
                <VsGlobalMenu
                    :active-site="configStore.activeSite === 'be'
                        ? 'https://businessevents.visitscotland.com/'
                        : 'https://www.visitscotland.com/'"
                />

                <BrManageMenuButton :menu="menuData" />

                <VsMeganav
                    href="/"
                    :menu-toggle-alt-text="configStore.getLabel('navigation.static', 'meganav-toggle-btn-alt-text')"
                    search-button-text=""
                    search-label-text=""
                    search-clear-button-text=""
                    search-close-button-text=""
                    :logo-alt-text="configStore.getLabel('navigation.static', 'meganav.logo-alt-text')"
                    :no-search="true"
                    :is-static="true"
                >
                    <template #mega-nav-top-menu-items>
                        <template
                            v-for="(menuItem, index) in menuItems"
                            :key="index"
                        >
                            <VsMegaNavStaticLink
                                :href="`/${menuItem.getUrl() ? menuItem.getUrl() : menuItem.model.name}`"
                            >
                                {{ menuItem.model.title }}
                            </VsMegaNavStaticLink>
                        </template>
                    </template>

                    <template #mega-nav-accordion-items>
                        <VsAccordion>
                            <template
                                v-for="(menuItem, index) in menuItems"
                                :key="index"
                            >
                                <VsMegaNavStaticLink
                                    class="vs-mega-nav-mobile"
                                    :href="
                                        menuItem.getUrl()
                                            ? menuItem.getUrl()
                                            : menuItem.model.name"
                                    :is-full-width="true"
                                >
                                    {{ menuItem.model.title }}
                                </VsMegaNavStaticLink>
                            </template>
                        </VsAccordion>
                    </template>
                </VsMeganav>
            </header>
        </div>

        <VsBanner
            v-if="banner"
            :close-btn-text="configStore.getLabel('essentials.global', 'close')"
        >
            <template #banner-text>
                <div v-html="banner.copy.value" />
            </template>

            <template #banner-cta>
                <VsLink
                    :href="banner.ctaLink.link"
                    :type="banner.ctaLink.type"
                >
                    {{ banner.ctaLink.label }}
                </VsLink>
            </template>
        </VsBanner>
    </div>
</template>

<script lang="ts" setup>
import { toRefs } from 'vue';
import type { Component, Page } from '@bloomreach/spa-sdk';
import { BrManageMenuButton } from '@bloomreach/vue3-sdk';

import useConfigStore from '~/stores/configStore.ts';

import {
    VsGlobalMenu,
    VsMeganav,
    VsMegaNavStaticLink,
    VsAccordion,
    VsBanner,
    VsLink,
} from '@visitscotland/component-library/components';

import VsBrSkipTo from '~/components/Base/VsBrSkipTo.vue';

const props = defineProps<{ component: Component, page: Page }>();

const { component, page } = toRefs(props);

let menu = {
    $ref: '',
};
let menuData : any = {
};
let menuItems : any[] = [];
let banner : any = null;

const configStore = useConfigStore();

if (page.value) {
    menu = component.value.getModels().menu;
    menuData = page.value.getContent(menu.$ref);
    menuItems = menuData.items;

    banner = component.value.getModels().banner;
    if (banner && banner.ctaLink) {
        banner.ctaLink.link = banner.ctaLink.link.replace('/site/resourceapi', '');
    }
}
</script>
