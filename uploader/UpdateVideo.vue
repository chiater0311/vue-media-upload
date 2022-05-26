<template>
    <div>
        <div class="">
            <div class="gallery width-100" :class="error?'red-border':''">
                <Loader
                    color="#0275d8" 
                    :active="loading" 
                    spinner="line-scale" 
                    background-color = 'rgba(255, 255, 255, .4)'
                />
                <div class="elements-wraper">
                    
                    <!--UPLOAD BUTTON-->
                    <div class="button-container image-margin">
                        <label for="videos-upload" class="videos-upload">
                            <svg
                                class="custum-icon"
                                xmlns="http://www.w3.org/2000/svg" 
                                width="1em" 
                                height="1em" 
                                preserveAspectRatio="xMidYMid meet" 
                                viewBox="0 0 24 24">
                                    <g fill="none">
                                        <path 
                                            d="M12 1C5.925 1 1 5.925 1 12s4.925 11 11 11s11-4.925 11-11S18.075 1 12 1zm1 15a1 1 0 1 1-2 0v-3H8a1 1 0 1 1 0-2h3V8a1 1 0 1 1 2 0v3h3a1 1 0 1 1 0 2h-3v3z" 
                                            fill="currentColor"/>
                                    </g>
                            </svg>
                        </label>     
                        <input @change="fileVideoChange" id="videos-upload" type="file" accept="video/mp4,video/x-m4v,video/*" multiple hidden>
                    </div>

                    <!--IMAGES PREVIEW-->
                    
                    <div v-for="(image, index) in saved_video" :key="index" class="video-container image-margin">
                        <video :src="media_file_path +'/'+ image.name" alt=""  class="videos_preview" controls controlsList="nodownload"></video>
                        <button @click="remove_saved_video(index)" class="close-btn" type="button">
                            <svg 
                                class='times-icon' 
                                xmlns="http://www.w3.org/2000/svg" 
                                width="0.65em" 
                                height="0.65em" 
                                preserveAspectRatio="xMidYMid meet" 
                                viewBox="0 0 352 512">
                                    <path 
                                        d="m242.72 256l100.07-100.07c12.28-12.28 12.28-32.19 0-44.48l-22.24-22.24c-12.28-12.28-32.19-12.28-44.48 0L176 189.28L75.93 89.21c-12.28-12.28-32.19-12.28-44.48 0L9.21 111.45c-12.28 12.28-12.28 32.19 0 44.48L109.28 256L9.21 356.07c-12.28 12.28-12.28 32.19 0 44.48l22.24 22.24c12.28 12.28 32.2 12.28 44.48 0L176 322.72l100.07 100.07c12.28 12.28 32.2 12.28 44.48 0l22.24-22.24c12.28-12.28 12.28-32.19 0-44.48L242.72 256z"
                                        fill="currentColor"    
                                    />
                            </svg>

                        </button>
                    </div>
                    <div v-for="(image, index) in added_video" :key="index" class="video-container image-margin">
                        <video :src="image.url" alt=""  class="videos_preview" controls controlsList="nodownload"></video>
                        <button @click="remove(index)" class="close-btn" type="button">
                            <svg 
                                class='times-icon' 
                                xmlns="http://www.w3.org/2000/svg" 
                                width="0.8em" 
                                height="0.8em" 
                                preserveAspectRatio="xMidYMid meet" 
                                viewBox="0 0 352 512">
                                    <path 
                                        d="m242.72 256l100.07-100.07c12.28-12.28 12.28-32.19 0-44.48l-22.24-22.24c-12.28-12.28-32.19-12.28-44.48 0L176 189.28L75.93 89.21c-12.28-12.28-32.19-12.28-44.48 0L9.21 111.45c-12.28 12.28-12.28 32.19 0 44.48L109.28 256L9.21 356.07c-12.28 12.28-12.28 32.19 0 44.48l22.24 22.24c12.28 12.28 32.2 12.28 44.48 0L176 322.72l100.07 100.07c12.28 12.28 32.2 12.28 44.48 0l22.24-22.24c12.28-12.28 12.28-32.19 0-44.48L242.72 256z"
                                        fill="currentColor"    
                                    />
                            </svg>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div v-if='error' id="media-required">
            <p class='red-text'>{{error}}</p>
        </div>
        <div v-for="(image, index) in added_video" :key="index" class="m-top">
            <input type="text" name="added_video[]" :value="image.name" hidden>
        </div>
        <div v-for="(image, index) in deleted_video" :key="index" class="m-top">
            <input type="text" name="deleted_video[]" :value="image.name" hidden>
        </div>
        <div v-if="added_video.length||saved_video.length" class="m-top">
            <input type="text" name="media" value="1" hidden>
        </div>
    </div>
</template>

<script>
    import Loader from './loader/index.vue';
    import axios from 'axios'

    export default {
        
        data(){
            return{
                added_video:[],
            
                saved_video:[],
                deleted_video:[],

                loading:true
            }
        },
        methods:{
            async fileVideoChange(event){
                this.loading=true
                let files = event.target.files
				console.log(files);
                for(var i=0; i < files.length; i++){
					
                    let formData = new FormData
                    let url = URL.createObjectURL(files[i])
                    formData.set('image', files[i])
                    const {data} = await axios.post(this.server, formData)
                        
                    this.added_video.push({url:url, name:data.name, size:files[i].size, type:files[i].type});
                }
                this.loading=false
                this.media_emit()
            },
            remove(index){
                this.added_video.splice(index,1)
                this.media_emit()
            },
            remove_saved_video(index){
                this.deleted_video.push({name:this.saved_video[index].name});
                this.saved_video.splice(index,1)
                this.media_emit()
            },
            media_emit(){
                this.$emit('added-video',this.added_video)
                this.$emit('deleted-video', this.deleted_video)
                this.$emit('saved-video', this.saved_video)
            }
            
        },
        props:{
            media_server:{
                type: String,
                required : true,
            },
            error:'',
            server: {
                type: String,
                default: '/api/upload',
            },
            media_file_path:{
                type: String,
                required : true,
            }
        },
        mounted() {
            axios.get(this.media_server)
                .then(response=>{
                    this.saved_video=response.data.media
                    this.loading = false
                    
                    this.media_emit()
                })
                
        },
        components:{Loader}
    }
        
</script>

<style scoped>
.image-wraper{
    min-height: 200px !important;
}

.gallery{
    background-color: #fbfbfb !important;
    border-radius: 5px !important;
    border-style: solid !important;
    border: 1px solid #bbbbbb !important;
    height: 85px !important;
    line-height: 1 !important;
    box-sizing: border-box !important;
    height: auto !important;
}

.videos-upload {
    background-color: #ffffff !important;
    border-radius: 5px !important;
    border: 1px dashed #ccc !important;
    display: inline-block !important;
    cursor: pointer !important;
    width: 165px !important;
    height: 88px !important;
}
.videos-upload:hover{
    background-color: #f1f1f1 !important;
}
.video-container{
    display: inline-table !important;
    height: 200px !important;
    width: 200px !important;
    display: flex !important;
}
.videos_preview {
    border-radius: 5px !important;
    border: 1px solid #ccc !important;
    display: inline-block !important;
    width: 200px !important;
    height: 200px !important;
    padding-top: -14px !important;
    transition: filter 0.1s linear;
    
}
.videos_preview:hover{
    filter: brightness(90%);
}

.button-container{
    display: inline-flex !important;
    height: 90px !important;
    width: 140px !important;
    margin-right: 0.25rem !important;
    margin-left: 0.25rem !important;
}

.close-btn{
    background: none !important;
	color:red !important;
	border: none !important;
	padding: 0px !important;
    margin:0px !important;
	font: inherit !important;
	cursor: pointer !important;
	outline: inherit !important;
    position: relative !important;
    right: 34px !important;
    top: -80px !important;
}
.times-icon{
    font-size: 3rem !important;
    padding: 0px !important;
    margin:0px !important;
}
.custum-icon{
    color: #00afca !important;
    font-size: 3rem !important;
    margin-top: 18px !important;
    margin-left: 44px !important;
    
}
.custum-icon:hover{
    color: #29818f !important;
}
.close-btn:hover{
    color: rgb(190, 39, 39) !important;
}


/* -------------------- */


.width-100 {
  width: 100% !important;
}
.red-border {
    border: 1px solid #dc3545 !important;
    border-color: #dc3545 !important;
}

.elements-wraper {
  padding: 1rem !important;
  display: flex !important;
  flex-wrap: wrap !important;

}
.align-center {
  text-align: center !important;
}
.m-top-1 {
  margin-top: 0.25rem !important;
}

.image-margin {
    margin-right: 0.25rem !important;
    margin-left: 0.25rem !important;
    margin-top: 0.25rem !important;
    margin-bottom: 0.25rem !important;
}
.red-text {
    color: #d82335;
}

</style>
