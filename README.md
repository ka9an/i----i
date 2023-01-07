# turbofb

[Edit on StackBlitz ⚡️](https://stackblitz.com/edit/github-stsqiv)

var ACCOUNT_ID = "d9c4a171c4a0d0a8e6f8204ae9ba0638";
const HEADERS = {
    "content-type": "application/json;charset=UTF-8",
    "X-Auth-Email": "vsamaru@gmail.com",
    "X-Auth-Key": "f55a79e9e2c2f7721b5775894df8ab955166a"
};
const getProjects = async ()=>{
    const URL = `https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/pages/projects`;
    const projects = await (await fetch(URL, {
        headers: HEADERS
    })).json();
    return projects.result;
};
const getDeployments = async (project)=>{
    const URL = `https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/pages/projects/${project}/deployments`;
    const deployments = await (await fetch(URL, {
        headers: HEADERS
    })).json();
    return deployments.result;
};
const deleteOldDeployments = async ()=>{
    const projects = await getProjects();
    for (const project of projects){
        const URL = `https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/pages/projects/${project}/deployments`;
        const deployments = await getDeployments(project.name);
        for (const deployment of deployments.splice(0, 40)){
            if ((Date.now() - new Date(deployment.created_on)) / 86400000 > 7) {
                await fetch(`${URL}/${deployment.id}`, {
                    method: "DELETE",
                    headers: HEADERS
                });
            }
        }
    }
    return new Response("OK", {
        status: 200
    });
};