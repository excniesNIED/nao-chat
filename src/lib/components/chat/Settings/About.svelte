<script lang="ts">
	import { getVersionUpdates } from '$lib/apis';
	import { getOllamaVersion } from '$lib/apis/ollama';
	import { WEBUI_BUILD_HASH, WEBUI_VERSION } from '$lib/constants';
	import { WEBUI_NAME, config, showChangelog } from '$lib/stores';
	import { compareVersion } from '$lib/utils';
	import { onMount, getContext } from 'svelte';

	import Tooltip from '$lib/components/common/Tooltip.svelte';

	const i18n = getContext('i18n');

	let ollamaVersion = '';

	let updateAvailable = null;
	let version = {
		current: '',
		latest: ''
	};

	const checkForVersionUpdates = async () => {
		updateAvailable = null;
		version = await getVersionUpdates(localStorage.token).catch((error) => {
			return {
				current: WEBUI_VERSION,
				latest: WEBUI_VERSION
			};
		});

		console.log(version);

		updateAvailable = compareVersion(version.latest, version.current);
		console.log(updateAvailable);
	};

	onMount(async () => {
		ollamaVersion = await getOllamaVersion(localStorage.token).catch((error) => {
			return '';
		});

		checkForVersionUpdates();
	});
</script>

<div class="flex flex-col h-full justify-between space-y-3 text-sm mb-6">
	<div class=" space-y-3 overflow-y-scroll max-h-[28rem] lg:max-h-full">

		{#if ollamaVersion}
			<hr class=" border-gray-100 dark:border-gray-850" />

			<div>
				<div class=" mb-2.5 text-sm font-medium">{$i18n.t('Ollama Version')}</div>
				<div class="flex w-full">
					<div class="flex-1 text-xs text-gray-700 dark:text-gray-200">
						{ollamaVersion ?? 'N/A'}
					</div>
				</div>
			</div>
		{/if}

		<hr class=" border-gray-100 dark:border-gray-850" />

		{#if $config?.license_metadata}
			<div class="mb-2 text-xs">
				{#if !$WEBUI_NAME.includes('Open WebUI')}
					<span class=" text-gray-500 dark:text-gray-300 font-medium">{$WEBUI_NAME}</span> -
				{/if}

				<span class=" capitalize">{$config?.license_metadata?.type}</span> license purchased by
				<span class=" capitalize">{$config?.license_metadata?.organization_name}</span>
			</div>
		{:else}
			<div class="flex space-x-1">
				<a href="https://discord.gg/5rJgQTnV4s" target="_blank">
					<img
						alt="Discord"
						src="https://img.shields.io/badge/Discord-Open_WebUI-blue?logo=discord&logoColor=white"
					/>
				</a>

				<a href="https://twitter.com/OpenWebUI" target="_blank">
					<img
						alt="X (formerly Twitter) Follow"
						src="https://img.shields.io/twitter/follow/OpenWebUI"
					/>
				</a>

				<a href="https://github.com/open-webui/open-webui" target="_blank">
					<img
						alt="Github Repo"
						src="https://img.shields.io/github/stars/open-webui/open-webui?style=social&label=Star us on Github"
					/>
				</a>
			</div>
		{/if}

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
			Emoji 图片由
			<a href="https://github.com/jdecked/twemoji" target="_blank">Twemoji</a>提供, 许可证是
			<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">CC-BY 4.0</a>.
		</div>

		<div>
			<pre
				class="text-xs text-gray-400 dark:text-gray-500">Copyright (c) {new Date().getFullYear()} <a
					href="https://sdnuroboticsailab.github.io"
					target="_blank"
					class="underline">NAO Chat（山东师范大学机器人实验室）</a
				>
All rights reserved.
由 <a
  					href="https://github.com/open-webui/open-webui"
  					target="_blank"
  					class="underline">Open WebUI </a
                                                 				>修改.

如果满足以下条件，则允许以源代码和二进制形式（无论是否经过修改）
重新分发和使用：

1. 源代码的再分发必须保留上述版权声明、此条件列表和以下免责声明。

2. 二进制形式的再分发必须在分发提供的文档和/或其他材料中复制上述
版权声明、此条件列表和以下免责声明。

3. 未经事先明确书面许可，不得使用版权保持器的名称或贡献者的名称来
支持或推广从本软件衍生的产品。

本软件按“原样”提供，不附带任何明示或默示的担保，包括但不限于适销性
担保、特定用途适用性担保及非侵权担保。在任何情况下，版权持有人或
贡献者均不对任何直接、间接、附带、特殊、惩戒性或后果性损害（
包括但不限于替代品或服务的采购、使用损失、数据损失或利润损失，或业务中断）
承担责任，无论该等损害基于何种责任理论产生，无论是合同责任、严格责任
或侵权行为(包括疏忽或其他原因)，即使已被告知可能发生此类损害。
</pre>
		</div>

		<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
			{$i18n.t('Created by')}
			<a
				class=" text-gray-500 dark:text-gray-300 font-medium"
				href="https://github.com/tjbck"
				target="_blank">Timothy J. Baek</a
			>
		</div>
	</div>
</div>
