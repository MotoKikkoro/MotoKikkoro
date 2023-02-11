# AWS���p��
## �T�[�o�[���X�T�[�r�X

```mermaid
sequenceDiagram
    actor �N���C�A���g
    participant API as API Gateway<br/>Rest API
    participant Lambda as Lambda Function<br/>�������W�b�N
    participant DataTable as Amazon DynamoDB<br/>�f�[�^�e�[�u��

    �N���C�A���g ->>+ API: �f�[�^�ꗗ�擾
    API ->>+ Lambda: �f�[�^�ꗗ�擾
    Lambda ->>+ DataTable: �f�[�^�ꗗ�擾
    DataTable -->>- Lambda: �f�[�^�ꗗ
    Lambda -->>- API: �f�[�^�ꗗ
    API -->>- �N���C�A���g: �f�[�^�ꗗ
```
- �]���^�Ƃ̔�r
    - API Gateway: Web�T�[�o�[
    - Lambda: App�T�[�o�[
    - DynamoDB: DB�T�[�o�[

## API Gateway
Lamda�Ȃǂ̊e��AWS�T�[�r�X��VPC���\�[�X�ɑ΂��Ă�API�G���h�|�C���g��񋟂���t���}�l�[�W�h�^�T�[�r�X
�F�؋@�\�A�X���b�g�����O��WAF�@�\�A�L���b�V���@�\�Ȃǂ��񋟁B

 - API�v���g�R���̎��

    |���|���e|
    |---|---|
    |REST|����URI�ŕ\�����郊�\�[�X�Ƃ��Ē�`�AHTTP���\�b�h�ő���|
    |HTTP API| Lambda�֐���R���e�i�̃V���v���ȊO��API��|
    |WebSocket|�N���C�A���g�ƃT�[�o�[�Ԃ̑o�����ʐM�������A��̃R�l�N�V�����Ōp���I�ȃf�[�^����M|

- �G���h�|�C���g�̎��

    |���|���e|
    |---|---|
    |�G�b�W�œK��|Public�Ɍ��J�A��U�G�b�W���P�[�V�����Ƀ��[�e�B���O|
    |���[�W����|Public�Ɍ��J�A���[�W�����ɒ��ڃ��[�e�B���O|
    |�v���C�x�[�g|Public�ɔ���J�AVPC������PrivateLink(VPC�G���h�|�C���g)�o�R�̂݃A�N�Z�X|

## Lambda
<font color="Green">**�T�[�o�[���X**</font>�R���s���[�e�B���O
�قƂ�ǂ��ׂĂ̎�ނ̃A�v���P�[�V������o�b�N�G���h�̃T�[�r�X�R�[�h��
�Ǘ���Ƃ��s�����ƂȂ����s�\�B�֐����`���Ă�����Ăяo�������Ŏg����B
- ���s���Ԃ͍Œ���15���B��̎��s�v�����ɁA��̎��s���Ŏ��s�����B
- �쐬���@
    - �l�X�Ȍ���iJava, Python, Ruby�Ȃǁj�ŊJ���\�B
    - �J����: AWS �Ǘ��R���\�[��, AWS CloudFormation, AS SAM CLI, AWS CDK 
    - ��VPC Lambda(�f�t�H���g�j��VPC Lamda���I���BVPC Lamda�̏ꍇ�AVPC���̃��\�[�X�ɃA�N�Z�X�ł���B
- ����
    - ���s�񐔂Ə���\�[�X�Ɋ�Â��]�ʉۋ�

## �R���e�i�T�[�r�X
- **Amazon Elastic Container Service(Amazon ECS)**: �R���e�i�����ꂽ�A�v���P�[�V���������s����B�V���v����API�R�[���𗘗p����Docker�Ή��A�v���P�[�V�����𐧌䂷��B
�N���E�h�ŃR���e�i��{�Ԋ����p���邽�߂̃I�[�P�X�g���[�^�[�B
    - �^�X�N��`�Ɋ�Â��A�R���e�i�Q���N�������B�^�X�N���R���e�i�͓����z�X�g�Ŏ��s
- **Amazon Elastic Kubernetes Service (Amazon EKS)**: Kubernetes�i�R���e�i�����ꂽ�A�v���P�[�V�������Ǘ�����v���b�g�t�H�[���j���_��ɊJ�n�A���s�A�X�P�[�����O����B�p�b�`�K�p�A�m�[�h�̃v���r�W���j���O�A�X�V������������B
- **Amazon Elastic Container Registry(ECR)**: �t���}�l�[�W�h�ȃv���C�x�[�g�R���e�i�C���[�W���W�X�g���B
Docker CLI���痘�p�BECS/EKS/Kubernetes�ȊO�̑��R���e�i�I�[�P�X�g���[�V������������p�ł���
- **AWS Fargate**: EC2�C���X�^���X�̃v���r�W������X�P�[���ȂǊǗ��}�{�ŁA���z�}�V�����ӎ������R���e�i���X�P�[�����O�ł���B
    - ����: �R���e�i�̋N�����Ԃ�g�p���\�[�X�ɉ����������ݒ�