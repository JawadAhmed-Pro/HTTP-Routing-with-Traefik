# HTTP-Routing-with-Traefik


This sample app accompanies the [Easy HTTP routing with Traefik](#) guide. The application is purposely kept simple to allow the focus to remain on the content of the guide - HTTP routing with Traefik. With Traefik, it is easy to use multiple services to split development tooling or provide additional visualization and troubleshooting tools.

Notice: This sample repo is intended to support the guide mentioned above. As such, the application code is purposely kept simple to keep the focus on the guide's content and should not be considered production ready.
##How the Application is working
![image](https://github.com/user-attachments/assets/7e5e8e57-d5b0-46c7-aba8-f45ce8901e3d)




## Try it out

1. Clone the Repository using:   `git clone  https://github.com/JawadAhmed-Pro/HTTP-Routing-with-Traefik.git`
   You will see These files and verfiy that all the files are present.
![image](https://github.com/user-attachments/assets/1bfac9d8-6c0a-40a8-a75f-8ec2e79df513)

3. Run `docker compose up --build`
4. Open the site at http://localhost. Requests to get data will go to `/api/message`, which is routed to the API service.
5. Open phpMyAdmin (database visualizer) at http://db.localhost

When you're done, run `docker compose down` to tear everything down.

## Contributing

Since this project is intended to support a specific use case guide, contributions are limited to bug fixes or security issues. If you have a question, feel free to open an issue!
