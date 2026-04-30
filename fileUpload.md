
# 📦 useFileUpload - Usage Guide

This guide explains how to use the `useFileUpload` composable for uploading files with:

- ✅ Chunk upload support  
- ✅ Progress tracking (per file + average)  
- ✅ Cancel (abort) upload per file  
- ✅ Custom data payload  

---

# 🚀 Basic Usage

```js
const res = await useFileUpload(url, options, onProgress);
```

# Parameters

| Name             | Type                     | Description                  |
| ---------------- | ------------------------ | ---------------------------- |
| `url`            | `string`                 | API endpoint                 |
| `options.files`  | `File \| File[]`         | File(s) to upload            |
| `options.data`   | `object`                 | Additional form data         |
| `options.signal` | `AbortSignal` (optional) | Global cancel signal         |
| `onProgress`     | `Function`               | Callback for upload progress |

# 📂 Example: Upload with Cancel Support
Template:
```vue
  <div
    v-if="!isLoading && documents.length > 0"
    class="w-full h-[240px] border-card rounded-2xl p-3"
    v-for="item in documents"
    :key="item?.tempId || item?.Id"
  >
    <!-- card -->
    <div
      class="w-full p-2 rounded-xl cursor-pointer relative overflow-hidden h-[calc(100%-50px)]"
      :class="[isPicture(item) ? `bg-[#f4f4f4] dark:bg-black` : ``]"
    >
      <div
        class="absolute w-[28px] h-[28px] flex justify-center bg-gray-200 dark:bg-gray-800 items-center cursor-pointer rounded-full"
        :class="[
          isPicture(item)
            ? `top-3 right-3 text-black dark:text-white`
            : `top-1 text-black dark:text-white right-2`,
        ]"
      >
        <OCBtnDropdown
          :items="getItemDropDown(item)"
          @click="
            (type) => {
              onClickBtnDropDownMenu(type, item);
            }
          "
        />
      </div>
      <!-- uploading progress -->
      <div
        v-if="item?.isUploading"
        class="absolute inset-0 bg-black/10 flex flex-col items-center justify-center gap-2 px-4"
      >
        <div class="text-sm font-medium text-primary">{{ item?.progress || 0 }}%</div>
        <div class="w-full h-2 bg-primary/20 rounded-full overflow-hidden">
          <div
            class="h-full bg-primary transition-all duration-300"
            :style="{ width: (item.progress || 0) + '%' }"
          ></div>
        </div>
      </div>
      <!-- error upload -->
      <div
        v-if="item?.error"
        class="absolute inset-0 bg-red-500/70 flex items-center justify-center transition-opacity duration-300"
        :class="{ 'opacity-0': item.fadeOut }"
      >
        <span class="text-white text-xs">Upload failed</span>
      </div>
      <!-- IMAGE -->
      <img
        v-if="isPicture(item)"
        :src="getImageUrl(item) ?? defaults"
        @click="OnclickImage(true, item)"
        class="object-scale-down w-full h-full"
        @error="fnErrorImg"
      />

      <!-- VIDEO -->
      <img
        v-else-if="isVideo(item)"
        :src="defaultVideo"
        @click="OnclickImage(false, item)"
        class="object-scale-down w-full h-full"
        @error="fnErrorImg"
      />

      <!-- OTHER FILE -->
      <img
        v-else
        :src="getSrcImage(item) ?? defaults"
        @click="OnclickImage(false, item)"
        class="object-scale-down w-full h-full"
        @error="fnErrorImg"
      />
    </div>
    <!-- title -->
    <div class="w-full h-[36px] flex items-center justify-center mt-[12px]">
      <!-- RENAME MODE -->
      <OCInput
        v-if="isRename && matchId?.Id === item.Id"
        v-model="item.Name"
        autofocus
        class="w-full text-[12px]"
        isRight
        @onBlur="onSubmitRename(item)"
      />

      <!-- NORMAL MODE -->
      <OCTooltip v-else :text="item?.Name + '.' + item?.Extension">
        <div
          class="w-full break-all ocs-truncate text-[14px] leading-[36px] cursor-default"
        >
          {{ getFileName(item) }}
        </div>
      </OCTooltip>
    </div>
  </div>
```

Javascript
```js
//1. Create Abort Controllers
let abortController = {};
function onCancelUpload(id) {
  if (!abortController[id]) return;

  abortController[id].abort();

  // Remove from UI list
  tempList.value = tempList.value.filter(d => d.tempId !== id);
}

//2. Upload Files
const tempList = ref([]);
const items = ref([]);
const documents = computed(() => [...tempList.value, ...items.value]);
const uploadDocument = async (files) => {
  try {
    const tempItems = files.map((file) => {
      const tempId = `temp-${Date.now()}-${Math.random()}`;

      // Create AbortController per file
      abortController[tempId] = new AbortController();

      return {
        ...file,
        Name: file.name.slice(0, file.name.lastIndexOf(".")),
        Extension: file.name.split(".").pop().toLowerCase(),
        fileTarget: file,
        tempId,
        signal: abortController[tempId].signal, // 👈 required for cancel
      };
    });

    tempList.value = tempItems;

    const res = await useFileUpload(
      api.student.document.upload,
      {
        files: tempItems,
        data: {
          StudentCode: infoStudent?.Code,
        },
      },

      // Progress callback
      ({ progressAverage, progress }) => {
        tempList.value = tempList.value.map((d, i) => ({
          ...d,
          progress: progress[i]?.progress || 0,
          isUploading: progress[i]?.loading || false,
        }));
      }
    );

    if (res?.error?.value) {
      throw res.error.value;
    }

    // Refresh after upload
    getDocumentByStudent(false);

  } catch (err) {
    toast.add({
      title: err?.Message || err,
      color: "red",
    });
  }
};
```

# 📊 Progress Response Format

```js
{
  progressAverage: number, // 0 - 100
  progress: [
    {
      progress: number,
      loading: boolean
    }
  ]
}
```

# ❌ Cancel Upload

1. Cancel a specific file upload:
```js
abortController[tempId].abort();
```

2. Cancel all file upload:
```js
const controller = new AbortController();
const res = await useFileUpload(
  api.student.document.upload,
  {
    files: tempItems,
    data: {
      StudentCode: infoStudent?.Code,
    },
    signal: controller.signal
  },
)

//To cancel
abortController.abort();
```

- ✔ Stops current chunk
- ✔ Prevents remaining chunks
- ✔ Cancels Axios request

# ✅ Summary
- Use AbortController per file
- Pass signal into useFileUpload
- Use onProgress to update UI
- Call .abort() to cancel upload
