<template>
    <div id="style-body">
        <div id="style-play-zone" v-show="currentZone === 'style-play-zone'">
            <div id="style-operate">
                <!-- 預覽區組件，顯示選擇的零件以及操作按鈕 -->
                <div class="style-view">
                    <!-- 傳遞顏色到SVG組件 -->
                    <div class="viewimagebox">
                        <originalGriddySrc :selectedSpotColor="selectedSpotColor" :selectedBodyColor="selectedBodyColor"
                            :selectedBellyColor="selectedBellyColor" 
                            :selectedEyesStaff="selectedEyesStaff"
                            :selectedEyesColor="selectedEyesColor" 
                            :selectedEarsStaff="selectedEarsStaff"
                            :selectedEarsColor="selectedEarsColor" 
                            :selectedAccessoriesStaff="selectedAccessoriesStaff"
                            :selectedAccessoriesColor="selectedAccessoriesColor"
                            :selectedBackgroundColor="selectedBackgroundColor" />
                    </div>

                </div>
                <div class="btnbox">
                    <button id="reset" @click="selectRandomOption" >
                        隨機選擇
                    </button>
                    <button id="random" @click="resetOptions" >
                        重置
                    </button>
                    <button id="setphoto" @click="toggleZone" >
                        確認送出
                    </button>
                </div>
            </div>
            <!-- 選項區，含頁籤 -->
            <div class="stylearea">
                <div class="tabs">
                    <button v-for="tab in tabs" :key="tab.name" :class="{ active: currentTab === tab.component }"
                        @click="selectTab(tab.component)" >
                        {{ tab.title }}
                    </button>
                </div>
                <!-- 頁籤內容 -->
                <div class="tab-content">
                    <component :is="currentTab" :default-body-color="selectedBodyColor"
                        :default-belly-color="selectedBellyColor" :default-spot-color="selectedSpotColor"
                        :default-eyes-color="selectedEyesColor" :default-eyes-staff="selectedEyesStaff"
                        :default-ears-color="selectedEarsColor" :default-ears-staff="selectedEarsStaff"
                        :default-accessories-color="selectedAccessoriesColor"
                        :default-accessories-staff="selectedAccessoriesStaff"
                        :default-background-color="selectedBackgroundColor" 
                        @body-color-selected="handleBodyColorChange"
                        @belly-color-selected="handleBellyColorChange" 
                        @spot-color-selected="handleSpotColorChange"
                        @eyes-color-selected="handleEyesColorChange" 
                        @eyes-staff-selected="handleEyesStaffChange"
                        @ears-color-selected="handleEarsColorChange" 
                        @ears-staff-selected="handleEarsStaffChange"
                        @accessories-staff-selected="handleAccessoriesStaffChange"
                        @accessories-color-selected="handleAccessoriesColorChange"
                        @background-color-selected="handleBackgroundColorChange">
                    </component>
                </div>
            </div>
        </div>
        <div id="show-zone" v-show="currentZone === 'show-zone'">
            <div class="final-show">
                <!-- 傳遞顏色到SVG組件 -->
                <div id="finalimagebox">
                    <originalGriddySrc :selectedSpotColor="selectedSpotColor" :selectedBodyColor="selectedBodyColor"
                        :selectedBellyColor="selectedBellyColor" :selectedEyesStaff="selectedEyesStaff"
                        :selectedEyesColor="selectedEyesColor" 
                        :selectedEarsStaff="selectedEarsStaff"
                        :selectedEarsColor="selectedEarsColor" 
                        :selectedAccessoriesStaff="selectedAccessoriesStaff"
                        :selectedAccessoriesColor="selectedAccessoriesColor"
                        :selectedBackgroundColor="selectedBackgroundColor" />
                </div>
                <span>最棒的Griddy粉墨登場！</span>
                <!-- <img v-if="griddyImage" :src="griddyImage" alt="Captured Griddy Content"> -->
                <div class="finalbtnbox">
                    <button id="setPicture" @click="setProfilePicture" >設為大頭貼</button>
                    <button id="goback" @click="toggleZone" >回上頁</button>
                    <button id="download" @click="griddyToImage" >下載圖片</button>
                </div>
                <SetMemPic ref="setMemPicModal" />
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';
import EyesComponent from '../components/EyesComponent.vue'
import EarsComponent from '../components/EarsComponent.vue';
import AccessoriesComponent from '../components/AccessoriesComponent.vue';
import BackgroundComponent from '../components/BackgroundComponent.vue';
import originalGriddySrc from '../components/NoneGriddy.vue';
import SkinComponent from '../components/SkinComponent.vue';

import { eyesStaffs } from "@/policy/color.js";
import { earsStaffs } from "@/policy/color.js" ;
import { accessoriesStaffs } from "@/policy/color.js";

import { unifiedColors } from "@/policy/color.js"
import html2canvas from 'html2canvas';
import SetMemPic from '../components/SetMemPic.vue'; // 燈箱組件
//import userStore from '@/stores/user'

export default {
    components: {
        originalGriddySrc, // svg組件，顯示選擇的變化
        SkinComponent, // 膚色區組件，用來切換膚色區的選擇
        EyesComponent, //眼睛區
        EarsComponent, //耳朵區
        AccessoriesComponent, //配件區
        BackgroundComponent, //背景顏色區
        SetMemPic, // 燈箱
    },
    data() {
        return {
            tabs: [
                { name: 'tab1', title: '膚色', component: 'SkinComponent' },
                { name: 'tab2', title: '眼睛', component: 'EyesComponent' },
                { name: 'tab3', title: '耳朵', component: 'EarsComponent' },
                { name: 'tab4', title: '配件', component: 'AccessoriesComponent' },
                { name: 'tab5', title: '背景', component: 'BackgroundComponent' },
            ],
            currentTab: 'SkinComponent', // 使用字符串替代 ref
            unifiedColors: unifiedColors,
            eyesStaffs: eyesStaffs,
            earsStaffs: earsStaffs,
            accessoriesStaffs: accessoriesStaffs,

            selectedBodyColor: unifiedColors[0],
            selectedBellyColor: unifiedColors[3],
            selectedSpotColor: unifiedColors[8],
            selectedEyesStaff: eyesStaffs[0],
            selectedEyesColor: unifiedColors[8],
            selectedEarsStaff: earsStaffs[0],
            selectedEarsColor: unifiedColors[0],
            selectedAccessoriesStaff: accessoriesStaffs[0],
            selectedAccessoriesColor: unifiedColors[7],
            selectedBackgroundColor: unifiedColors[18],
            currentZone: 'style-play-zone',
            griddyImage: null, // 存储转换后的图像
            //userStoreData: userStore(),
        };
    },
    methods: {
        selectTab(componentName) {
            this.currentTab = componentName;  // 更新當前標籤頁名稱
            console.log('標籤選擇:', componentName);
            // localStorage.removeItem('userId');
        },
        toggleZone() {
            this.currentZone = this.currentZone === 'style-play-zone' ? 'show-zone' : 'style-play-zone';
        },
        griddyToImage() {
            const elementToCapture = document.getElementById('finalimagebox');
            const scale = 4; // 設置畫布分辨率增加的倍數，例如 2 表示 2x 分辨率

            const options = {
                scale: scale,
                width: elementToCapture.offsetWidth,
                height: elementToCapture.offsetHeight,
                useCORS: true // 允許加載跨域圖片
            };

            html2canvas(elementToCapture, options).then(canvas => {
                const downloadLink = document.createElement('a');
                const date = new Date();
                const timestamp = date.getFullYear().toString() +
                    (date.getMonth() + 1).toString().padStart(2, '0') +
                    date.getDate().toString().padStart(2, '0') + '_' +
                    date.getHours().toString().padStart(2, '0') +
                    date.getMinutes().toString().padStart(2, '0') +
                    date.getSeconds().toString().padStart(2, '0');
                const filename = `OMG${timestamp}.png`;

                downloadLink.href = canvas.toDataURL('image/png');
                downloadLink.download = filename; // 圖片文件名

                // 模擬點擊鏈接以觸發下載
                document.body.appendChild(downloadLink);
                downloadLink.click();
                document.body.removeChild(downloadLink);
            });
        },
        setProfilePicture() {
            const elementToCapture = document.getElementById('finalimagebox');
            html2canvas(elementToCapture, { useCORS: true }).then(canvas => {
                const imageData = canvas.toDataURL('image/png');
                const userDataStr = localStorage.getItem('userDataStr'); // 从localStorage获取会员数据字符串

                if (!userDataStr) {
                    alert('請先登入。'); // 如果userDataStr不存在，彈出提示
                    return; // 關鍵：提早返回以防止繼續向下運行
                }

                const userData = JSON.parse(userDataStr); // 解析成JSON对象

                // 避免當userData或userData.mem_id為null時嘗試訪問屬性
                if (!userData || !userData.mem_id) {
                    alert('請先登入。'); // 進ㄧ步檢查，彈出提示
                    return; // 關鍵：提早返回
                }

                const memId = userData.mem_id; // 從用戶資訊中解構mem_id

                let formData = new FormData();
                formData.append('profile_pic', imageData); // 将base64图片数据转换成Blob并添加
                formData.append('user_id', memId); // 添加使用者ID

                for (let [key, value] of formData.entries()) {
                    console.log(`${key}: ${value}`);
                }
                console.log(userDataStr);
                console.log(userData);


                this.uploadProfilePic(formData); // 调用下面定义的方法来处理上传
                this.$refs.setMemPicModal.showModal();

            });

        },
        // 這個函數用來上傳圖片到網站上
        // uploadProfilePic(formData) {
        //     // 使用fetch發送請求到指定的網址，並上傳一些資料
        //     fetch(`${import.meta.env.VITE_API_URL}/uploadProfilePic.php`, {
        //         method: 'POST', // 表示這是一個"POST"請求，用來上傳資料
        //         body: formData, // 把準備好的圖片資料發送出去
        //     })
        //         .then(response => {
        //             // .then()是用來處理伺服器回應的。當伺服器回應後，這裡的代碼就會執行。

        //             if (!response.ok) {
        //                 // response.ok是一個布林值，如果伺服器回應的狀態碼是200-299之間，它就是true，表示"一切OK"
        //                 throw new Error('網絡響應非OK'); // 如果不是OK，就報錯
        //             }
        //             return response.json(); // 如果一切OK，就把伺服器回應的內容轉換成JavaScript對象，方便我們使用
        //         })
        //         .then(data => {
        //             if (!data.error) {
        //                 console.log('上传成功:', data.msg);

        //                 // 从localStorage获取当前用户数据
        //                 let userDataStr = localStorage.getItem('userDataStr');
        //                 let userData = JSON.parse(userDataStr);

        //                 // 更新userData中的mem_profile为后端返回的新大头贴路径
        //                 userData.mem_profile = data.newProfilePicPath;

        //                 this.userStoreData.updateUserData({ ...userData })

        //                 // 将更新后的用户数据保存回localStorage
        //                 // localStorage.setItem('userDataStr', JSON.stringify(userData));

        //                 // 这里可以添加其他逻辑，比如更新页面上显示的大头贴图片等
        //             } else {
        //                 console.error('上传失败:', data.msg);
        //             }
        //         })
        //         .catch(error => { // 如果在發送請求或處理回應的過程中出現任何錯誤
        //             console.error('上傳錯誤:', error); // 就在控制台顯示出錯的訊息
        //         });
        // },
        fullImageUrl(memProfile) {
            return `${import.meta.env.VITE_API_URL}/images/mem/${memProfile}`;
        },
        showProfilePicModal() {
            this.$refs.setMemPicModal.showModal();
        },

        handleBodyColorChange(bodyColor) {
            this.selectedBodyColor = bodyColor;
            console.log("被選擇的軀幹顏色：", this.selectedBodyColor);
        },
        handleBellyColorChange(bellyColor) {
            this.selectedBellyColor = bellyColor;
            console.log("被選擇的肚肚顏色：", this.selectedBellyColor);
        },
        handleSpotColorChange(spotColor) {
            this.selectedSpotColor = spotColor;
            console.log("被選擇的斑點顏色：", this.selectedSpotColor);
        },
        handleEyesStaffChange(eyesStaff) {
            this.selectedEyesStaff = eyesStaff;
            console.log("被選擇的眼鏡零件：", this.selectedEyesStaff);
        },
        handleEyesColorChange(eyesColor) {
            this.selectedEyesColor = eyesColor;
            console.log("被選擇的眼鏡顏色：", this.selectedEyesColor);
        },
        handleEarsStaffChange(earsStaff) {
            this.selectedEarsStaff = earsStaff;
            console.log("被選擇的耳朵零件：", this.selectedEarsStaff);
        },
        handleEarsColorChange(earsColor) {
            this.selectedEarsColor = earsColor;
            console.log("被選擇的耳朵顏色：", this.selectedEarsColor);
        },
        handleAccessoriesStaffChange(accessoriesStaff) {
            this.selectedAccessoriesStaff = accessoriesStaff;
            console.log("被選擇的配件零件：", this.selectedAccessoriesStaff);
        },
        handleAccessoriesColorChange(accessoriesColor) {
            this.selectedAccessoriesColor = accessoriesColor;
            console.log("被選擇的配件顏色：", this.selectedAccessoriesColor);
        },
        handleBackgroundColorChange(backgroundColor) {
            this.selectedBackgroundColor = backgroundColor;
            console.log("被選擇的背景顏色：", this.selectedBackgroundColor);
        },

        selectRandomOption() {
            // 為身體顏色選擇一個隨機選項
            this.selectedBodyColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為肚子顏色選擇一個隨機選項
            this.selectedBellyColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為斑點顏色選擇一個隨機選項
            this.selectedSpotColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為眼睛部件選擇一個隨機選項
            this.selectedEyesStaff = this.eyesStaffs[Math.floor(Math.random() * this.eyesStaffs.length)];

            // 為眼睛顏色選擇一個隨機選項
            this.selectedEyesColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為耳朵部件選擇一個隨機選項
            this.selectedEarsStaff = this.earsStaffs[Math.floor(Math.random() * this.earsStaffs.length)];

            // 為耳朵顏色選擇一個隨機選項
            this.selectedEarsColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為配件部件選擇一個隨機選項
            this.selectedAccessoriesStaff = this.accessoriesStaffs[Math.floor(Math.random() * this.accessoriesStaffs.length)];

            // 為配件顏色選擇一個隨機選項
            this.selectedAccessoriesColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];

            // 為背景顏色選擇一個隨機選項
            this.selectedBackgroundColor = this.unifiedColors[Math.floor(Math.random() * this.unifiedColors.length)];
        },
        resetOptions() {
            // 重置所有選項到預設值
            this.selectedBodyColor = this.unifiedColors[0];
            this.selectedBellyColor = this.unifiedColors[3];
            this.selectedSpotColor = this.unifiedColors[8];
            this.selectedEyesColor = this.unifiedColors[8];
            this.selectedEarsColor = this.unifiedColors[0];
            this.selectedAccessoriesColor = this.unifiedColors[7];
            this.selectedBackgroundColor = this.unifiedColors[18];


            // 如果有部件選項也需要重置，可以類似地添加這裡
            this.selectedEyesStaff = this.eyesStaffs[0];
            this.selectedEarsStaff = this.earsStaffs[0];
            this.selectedAccessoriesStaff = this.accessoriesStaffs[0];
            // 確保更新所有相關的部件選項
        }
    }
};
</script>