FROM alpine:3
RUN touch 1.txt

## image layer concepts RUN leads an extra layer

FROM alpine:3
RUN touch 1.txt
RUN touch 2.txt 

FROM alpine:3
RUn touch 1.txt && touch 2.txt
