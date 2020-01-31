node{

    def docker_host="www.j116.cn"
    def docker_img_name="${docker_host}/sso/boot1"
    def tag_name="prod-${BUILD_ID}"
	stage('拉取代码') {
		checkout scm
	}
	stage('maven编译') {
		dir('./') {
		sh '/usr/local/maven3/bin/mvn  clean install -DskipTests=true'
       }
	}
	stage('镜像构建') {
	    dir('./') {
            sh "docker build -t ${docker_img_name}:${tag_name} . "
            withCredentials([usernamePassword(credentialsId: 'harbor', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
            sh "docker login -u ${dockerUser} -p ${dockerPassword}  ${docker_host}"
            sh "docker push ${docker_img_name}:${tag_name}"
        }    
       }
	}
	stage('运行镜像') {
         dir('./') {
             sh "sed -i 's/www.j116.cn\\/sso\\/boot1:prod-xxx/www.j116.cn\\/sso\\/boot1:${tag_name}/g' deployment.yaml"    
         sh '''
            ssh c32 mkdir -p /data/k8s2
            scp -rp k8s/* c32:/data/k8s2/    
            ssh  c32  sudo kubectl apply -f  /data/k8s2/deployment.yaml
           '''  
    }
	}
}
