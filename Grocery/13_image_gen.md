# Image Gen

https://github.com/git-lfs/git-lfs
https://huggingface.co/CompVis/stable-diffusion-v1-4


Attempting to generate imgages.

Will download model weights and learn how to use a model!

How to guide:
https://huggingface.co/blog/stable_diffusion

## Repos

https://github.com/huggingface/diffusers -- Gonna try the CompVis version.
https://github.com/CompVis/stable-diffusion

https://docs.conda.io/en/latest/miniconda.html

Needed to install miniconda...


https://stackoverflow.com/questions/49154899/resolvepackagenotfound-create-env-using-conda-and-yml-file-on-macos/50499436#50499436

Also needed a OSX M1 specific environment file...

## Memory Constraints

Played around with a lot of tutorials on my desktop. Pretty certain that there is an unstated DDR RAM minimum. I'm at 8gb but it seems to be crashing die to OOM process killer. Purchased larger ram (will be useful for other projects) and hopefully it'll work once upgraded.

## Ending Process
Wrote a script that calls the API. This worked without many changes, (added RETIT list so it's less tied to the REPL and would ideally make it possible at some point to do something cool hosting the webapp separate from the image generator. But for now I didn't do this)

Then a basic webpage where I clicked the image I wanted to keep and it moved it into a special directory. 

Overall its somewhat fragile, but works very well for my goals & purposes.
